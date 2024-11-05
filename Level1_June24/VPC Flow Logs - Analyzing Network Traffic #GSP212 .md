# GSP212
## Run in cloudshell
```cmd
export ZONE=
```
```cmd
export REGION=${ZONE::-2}
gcloud compute networks create vpc-net --subnet-mode custom
gcloud compute networks subnets create vpc-subnet \
--network=vpc-net \
--region=$REGION \
--range=10.1.3.0/24 \
--enable-flow-logs
cat > hustlers.json << EOF
{
  "allowed": [
    {
      "IPProtocol": "tcp",
      "ports": [
        "80",
        "22"
      ]
    }
  ],
  "description": "",
  "direction": "INGRESS",
  "disabled": false,
  "enableLogging": false,
  "kind": "compute#firewall",
  "logConfig": {
    "enable": false
  },
  "name": "allow-http-ssh",
  "network": "projects/$DEVSHELL_PROJECT_ID/global/networks/vpc-net",
  "priority": 1000,
  "selfLink": "projects/$DEVSHELL_PROJECT_ID/global/firewalls/allow-http-ssh",
  "sourceRanges": [
    "0.0.0.0/0"
  ],
  "targetTags": [
    "http-server"
  ]
}
EOF
export ACCESS_TOKEN=$(gcloud auth application-default print-access-token)
curl -X POST \
-H "Authorization: Bearer $ACCESS_TOKEN" \
-H "Content-Type: application/json" \
-d @hustlers.json \
"https://compute.googleapis.com/compute/v1/projects/$DEVSHELL_PROJECT_ID/global/firewalls"
gcloud compute instances create web-server \
--network=vpc-net \
--subnet=vpc-subnet \
--zone=$ZONE \
--machine-type=e2-micro \
--tags=ssh,http,rules,http-server
sleep 40
gcloud compute ssh --zone=$ZONE web-server --quiet \
--command="
sudo apt-get update;
sudo apt-get install apache2 -y;
echo '<!doctype html><html><body><h1>Hello World!</h1></body></html>' | sudo tee /var/www/html/index.html;
exit;"
DATASET_ID=bq_vpcflows
bq mk --location=$REGION $DEVSHELL_PROJECT_ID:$DATASET_ID
DATASET_ID=bq_vpcflows
bq mk --location=$REGION $DEVSHELL_PROJECT_ID:$DATASET_ID
echo -e "\e[34mhttps://console.cloud.google.com/logs/router/sink;query=resource.type%3D%22gce_subnetwork%22%0Alog_name%3D%22projects%2F$DEVSHELL_PROJECT_ID%2Flogs%2Fcompute.googleapis.com%252Fvpc_flows%22?project=$DEVSHELL_PROJECT_ID\e[0m"
```
> Click the given link in cloudshell <br/>
> Set the name to `vpc-flows`<br/>
> Click Next<br/>
> Set the Sink Service to `BigQuery Dataset`<br/>
> Set BigQuery dataset to `bq_vpcflows`<br/>
> Click Create Sink<br/>
```cmd
export MY_SERVER=$(gcloud compute instances describe web-server --zone=$ZONE --format='get(networkInterfaces[0].accessConfigs[0].natIP)')
for ((i=1;i<=50;i++)); do curl $MY_SERVER; done
```


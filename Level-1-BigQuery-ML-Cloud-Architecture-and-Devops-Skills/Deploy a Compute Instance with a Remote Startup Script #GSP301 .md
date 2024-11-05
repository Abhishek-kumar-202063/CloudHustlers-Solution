# GSP301
## Run in cloudshell
```cmd
export ZONE=
```
```cmd
export PROJECT_ID=$(gcloud config get-value project)
gsutil mb -b off gs://$PROJECT_ID
gsutil cp gs://spls/gsp301/install-web.sh gs://$PROJECT_ID/install-web.sh
gcloud compute instances add-metadata lab-monitor \
    --metadata startup-script-url=gs://$PROJECT_ID/install-web.sh \
    --zone $ZONE
gcloud compute firewall-rules create http-fw-rule \
    --allow=tcp:80 \
    --source-ranges=0.0.0.0/0 \
    --target-tags=allow-http-traffic \
    --network=default
gcloud compute instances add-tags lab-monitor --tags=allow-http-traffic --zone=$ZONE
gcloud compute instances reset lab-monitor --zone=$ZONE
```
```cmd
gcloud compute ssh lab-monitor --zone=$ZONE --quiet
```
```cmd
sudo apt update
sudo apt install apache2
export CLOUDHUSTLERS=$(curl -s http://metadata.google.internal/computeMetadata/v1/instance/network-interfaces/0/access-configs/0/external-ip -H "Metadata-Flavor: Google")
curl http://$CLOUDHUSTLERS
```

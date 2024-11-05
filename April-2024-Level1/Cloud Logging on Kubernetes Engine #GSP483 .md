# GSP483
## Run in cloudshell
```cmd
export ZONE=
```
```cmd
export REGION=${ZONE::-2}
gcloud config set compute/region $REGION
gcloud config set compute/zone $ZONE
git clone https://github.com/GoogleCloudPlatform/gke-logging-sinks-demo
cd gke-logging-sinks-demo/terraform
sed -i 's/  version = "~> 2.11.0"/  version = "~> 2.19.0"/g' provider.tf
sed -i 's/  filter      = "resource.type = container"/  filter      = "resource.type = k8s_container"/g' main.tf
cd ..
make create
bq query --use_legacy_sql=false '
SELECT * FROM `'$DEVSHELL_PROJECT_ID'.gke_logs_dataset.diagnostic_log_*`
WHERE _TABLE_SUFFIX BETWEEN FORMAT_DATE("%Y%m%d", CURRENT_DATE() - 1) AND FORMAT_DATE("%Y%m%d", CURRENT_DATE()) 
'
```

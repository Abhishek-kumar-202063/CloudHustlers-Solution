# GSP164
[![](https://api.pointscounter.me/servers/img/subscribe)](https://www.youtube.com/@CloudHustlers)
## Run in cloudshell
```cmd
gsutil cp gs://spls/gsp164/endpoints-quickstart.zip .
unzip endpoints-quickstart.zip
cd endpoints-quickstart/scripts
./deploy_api.sh
./deploy_app.sh
./query_api.sh
./query_api.sh JFK
./deploy_api.sh ../openapi_with_ratelimit.yaml
./deploy_app.sh
gcloud alpha services api-keys create --display-name="CloudHustlers" 
KEY_NAME=$(gcloud alpha services api-keys list --format="value(name)" --filter "displayName=CloudHustlers")
export API_KEY=$(gcloud alpha services api-keys get-key-string $KEY_NAME --format="value(keyString)")
./query_api_with_key.sh $API_KEY
./generate_traffic_with_key.sh $API_KEY
./query_api_with_key.sh $API_KEY
```

# GSP037
[![]()](https://www.youtube.com/@CloudHustlers)
## CREATE AN API_KEY > APIs & Services > Credentials > Create Credentials>API key
## Run in cloudshell
```cmd
gcloud alpha services api-keys create --display-name="CloudHustlers" 
KEY_NAME=$(gcloud alpha services api-keys list --format="value(name)" --filter "displayName=CloudHustlers")
export API_KEY=$(gcloud alpha services api-keys get-key-string $KEY_NAME --format="value(keyString)")
export PROJECT_ID=$(gcloud config list --format 'value(core.project)')
gsutil mb gs://$PROJECT_ID
git clone https://github.com/siddharth7000/practice.git
gsutil cp practice/donuts.png gs://$PROJECT_ID
gsutil cp practice/selfie.png gs://$PROJECT_ID
gsutil cp practice/city.png gs://$PROJECT_ID
gsutil acl ch -u AllUsers:R gs://$PROJECT_ID/donuts.png
gsutil acl ch -u AllUsers:R gs://$PROJECT_ID/selfie.png
gsutil acl ch -u AllUsers:R gs://$PROJECT_ID/city.png
```

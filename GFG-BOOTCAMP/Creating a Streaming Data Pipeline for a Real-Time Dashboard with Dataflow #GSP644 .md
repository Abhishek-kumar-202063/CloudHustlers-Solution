# GSP644
>🚨 [PLEASE SUBSCRIBE OUR CHANNEL CLOUDHUSTLER](https://www.youtube.com/@cloudhustlers) **&** 
### Run in cloudhsell (Copy zone from task 3 step 9 `inside the command`)
```cmd
export REGION=
```
```cmd
gcloud services enable run.googleapis.com
git clone https://github.com/rosera/pet-theory.git
cd pet-theory/lab03
sed -i '/"scripts": {/a\    "start": "node index.js",' package.json
npm install express
npm install body-parser
npm install child_process
npm install @google-cloud/storage
gcloud builds submit \
  --tag gcr.io/$GOOGLE_CLOUD_PROJECT/pdf-converter
gcloud run deploy pdf-converter \
  --image gcr.io/$GOOGLE_CLOUD_PROJECT/pdf-converter \
  --platform managed \
  --region $REGION \
  --no-allow-unauthenticated \
  --max-instances=1
SERVICE_URL=$(gcloud beta run services describe pdf-converter --platform managed --region $REGION --format="value(status.url)")
curl -X POST $SERVICE_URL
curl -X POST -H "Authorization: Bearer $(gcloud auth print-identity-token)" $SERVICE_URL
gsutil mb gs://$GOOGLE_CLOUD_PROJECT-upload
gsutil mb gs://$GOOGLE_CLOUD_PROJECT-processed
gsutil notification create -t new-doc -f json -e OBJECT_FINALIZE gs://$GOOGLE_CLOUD_PROJECT-upload
gcloud iam service-accounts create pubsub-cloud-run-invoker --display-name "PubSub Cloud Run Invoker"
gcloud beta run services add-iam-policy-binding pdf-converter --member=serviceAccount:pubsub-cloud-run-invoker@$GOOGLE_CLOUD_PROJECT.iam.gserviceaccount.com --role=roles/run.invoker --platform managed --region $REGION
PROJECT_NUMBER=$(gcloud projects list --format=json | jq -r '.[] | select(.name == "'$DEVSHELL_PROJECT_ID'") | .projectNumber')
gcloud projects add-iam-policy-binding $GOOGLE_CLOUD_PROJECT --member=serviceAccount:service-$PROJECT_NUMBER@gcp-sa-pubsub.iam.gserviceaccount.com --role=roles/iam.serviceAccountTokenCreator
gcloud beta pubsub subscriptions create pdf-conv-sub --topic new-doc --push-endpoint=$SERVICE_URL --push-auth-service-account=pubsub-cloud-run-invoker@$GOOGLE_CLOUD_PROJECT.iam.gserviceaccount.com
gcloud builds submit \
  --tag gcr.io/$GOOGLE_CLOUD_PROJECT/pdf-converter
gcloud run deploy pdf-converter \
  --image gcr.io/$GOOGLE_CLOUD_PROJECT/pdf-converter \
  --platform managed \
  --region $REGION \
  --memory=2Gi \
  --no-allow-unauthenticated \
  --max-instances=1 \
  --set-env-vars PDF_BUCKET=$GOOGLE_CLOUD_PROJECT-processed

```


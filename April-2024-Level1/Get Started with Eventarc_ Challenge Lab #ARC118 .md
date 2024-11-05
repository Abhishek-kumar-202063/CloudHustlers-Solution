# ARC118
## Run in cloudshell
```cmd
export REGION=
```
```cmd
gcloud services enable \
run.googleapis.com \
eventarc.googleapis.com
gcloud pubsub topics create $DEVSHELL_PROJECT_ID-topic
gcloud pubsub subscriptions create $DEVSHELL_PROJECT_ID-topic-sub --topic $DEVSHELL_PROJECT_ID-topic
gcloud run deploy pubsub-events \
--region=$REGION \
--image=gcr.io/cloudrun/hello \
--allow-unauthenticated \
--platform=managed
gcloud eventarc triggers create pubsub-events-trigger \
--location=$REGION \
--transport-topic=$DEVSHELL_PROJECT_ID-topic \
--destination-run-region=$REGION \
--destination-run-service=pubsub-events \
--event-filters="type=google.cloud.pubsub.topic.v1.messagePublished"
gcloud pubsub topics publish $DEVSHELL_PROJECT_ID-topic \
--message="Subscribe To CloudHustlers"
```

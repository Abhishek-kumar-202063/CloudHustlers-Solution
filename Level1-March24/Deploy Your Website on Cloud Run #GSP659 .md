# GSP659
[![]()](https://www.youtube.com/@CloudHustlers)
## Run in cloudshell
```cmd
export REGION=
```
```cmd
gcloud services enable artifactregistry.googleapis.com \
cloudbuild.googleapis.com \
run.googleapis.com
git clone https://github.com/googlecodelabs/monolith-to-microservices.git
cd ~/monolith-to-microservices
./setup.sh
cd ~/monolith-to-microservices/monolith
gcloud artifacts repositories create monolith-demo --location=$REGION --repository-format=docker
gcloud auth configure-docker $REGION-docker.pkg.dev
gcloud builds submit --tag $REGION-docker.pkg.dev/$DEVSHELL_PROJECT_ID/monolith-demo/monolith:1.0.0
gcloud run deploy monolith --image $REGION-docker.pkg.dev/$DEVSHELL_PROJECT_ID/monolith-demo/monolith:1.0.0 --allow-unauthenticated --region $REGION
gcloud run deploy monolith --image $REGION-docker.pkg.dev/$DEVSHELL_PROJECT_ID/monolith-demo/monolith:1.0.0 --allow-unauthenticated --region $REGION --concurrency 1
gcloud run deploy monolith --image $REGION-docker.pkg.dev/$DEVSHELL_PROJECT_ID/monolith-demo/monolith:1.0.0 --allow-unauthenticated --region $REGION --concurrency 80
cd ~/monolith-to-microservices/react-app/src/pages/Home
mv index.js.new index.js
cd ~/monolith-to-microservices/react-app
npm run build:monolith
cd ~/monolith-to-microservices/monolith
gcloud builds submit --tag $REGION-docker.pkg.dev/$DEVSHELL_PROJECT_ID/monolith-demo/monolith:2.0.0
gcloud run deploy monolith --image $REGION-docker.pkg.dev/$DEVSHELL_PROJECT_ID/monolith-demo/monolith:2.0.0 --allow-unauthenticated --region $REGION
```

# GSP484
>🚨 [PLEASE SUBSCRIBE OUR CHANNEL CLOUDHUSTLER](https://www.youtube.com/@cloudhustlers) 
###  Run in Cloudshell
```cmd
git clone https://github.com/GoogleCloudPlatform/gke-tracing-demo
cd gke-tracing-demo
cd terraform
gcloud config set compute/region us-central1
gcloud config set compute/zone us-central1-a
sed -i '/version = "~> 2.10.0"/d' provider.tf
terraform init
../scripts/generate-tfvars.sh
terraform plan
terraform apply --auto-approve
kubectl apply -f tracing-demo-deployment.yaml
echo http://$(kubectl get svc tracing-demo -n default -o jsonpath='{.status.loadBalancer.ingress[0].ip}')?string=CustomMessage
```
> Click on the link from last line in terminal

>Continue to site  

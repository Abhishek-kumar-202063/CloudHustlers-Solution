# GSP122
## Run in cloudshell
```cmd
export ZONE=
```
```cmd
export REGION=${ZONE::-2}
gcloud config set compute/region $REGION
gcloud compute instances create cloudhustlers \
--zone $ZONE \
--image-family centos-7 \
--image-project centos-cloud \
--machine-type e2-micro \
--tags http-server
sleep 5
gcloud compute ssh cloudhustlers --zone=$ZONE --quiet
```
```cmd
sudo tee -a /etc/yum.repos.d/google-cloud-sdk.repo << EOM
[google-cloud-sdk]
name=Google Cloud SDK
baseurl=https://packages.cloud.google.com/yum/repos/cloud-sdk-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=0
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg
       https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOM
sudo yum install google-cloud-sdk -y && gcloud init --console-only
```
> Press 2 </br>
> Press Y </br>
> Go to link and copy paste authentication code </br>
> Press 1

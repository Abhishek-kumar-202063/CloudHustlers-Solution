![hii guys ](https://drive.google.com/uc?export=view&id=1ua6GTiZhdGNUDiPl4eRQESlRobpdQOO1)

# GSP066
## Run in cloudshell
```cmd
export ZONE=
```
```cmd
gcloud config set compute/zone $ZONE
gcloud container clusters create awwvision \
--num-nodes=2 \
--scopes=cloud-platform
gcloud container clusters get-credentials awwvision
gsutil -m cp -r gs://spls/gsp066/cloud-vision .
cd cloud-vision/python/awwvision
make all
```

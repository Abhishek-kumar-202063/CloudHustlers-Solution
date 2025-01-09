![API Gateway Banner](https://github.com/Abhiraj-1604/gcsbucket/blob/cd5a79c3b8251e85303f240c57d6a25411449897/channels4_banner.jpg)
![hii guys ](https://abhishek-kumar-202063.github.io/test/banner-github.html)



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

# GSP643
## Copy the IDE and open in incognito
## Open terminal and Paste this command
```cmd
git clone https://github.com/rosera/pet-theory.git
cd ~/pet-theory/lab02
npm i && npm audit fix --force
gcloud config set compute/region us-central1
gcloud app create --region=us-central
gcloud firestore databases create --region=us-central
```
____
### Login your Firebase console
### Add project > Select GCP id > Accept terms and cond > confirm Plan > continue > continue > Add Firebase
### Select the web icon > Pet Theory > check the box > Register app > Next > Next > Continue to console
### Build > Authentication > Get started > Google > Enable > select your lab > Save 
### Build > Firestore Database > create database > Next > us-east1 > enable 
### start collection > customers > Doc id = "username" > field = email >value ="username"
### Add field > field = name >value =cloudhustlers
### Add field > field = phone >value =8668979879 > save
____

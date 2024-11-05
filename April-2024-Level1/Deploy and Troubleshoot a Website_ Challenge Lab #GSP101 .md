# GSP101
## Run in cloudshell (Press n when asked)
```cmd
export INSTANCE_NAME_HUSTLER=
```
```cmd
export ZONE_HUSTLER=
```
```cmd
gcloud compute firewall-rules create http-ingress --allow=tcp:80 --source-ranges 0.0.0.0/0 --target-tags http-server --network default
gcloud compute instances create $INSTANCE_NAME_HUSTLER --zone=$ZONE_HUSTLER --machine-type=e2-medium --tags=http-server,https-server --image=projects/debian-cloud/global/images/debian-10-buster-v20220406 --metadata=startup-script=\#\!\ /bin/bash$'\n'apt-get\ update$'\n'apt-get\ install\ apache2\ -y$'\n'service\ --status-all$'\n'
gcloud compute instances describe $INSTANCE_NAME_HUSTLER --format='get(networkInterfaces[0].accessConfigs[0].natIP)'
```

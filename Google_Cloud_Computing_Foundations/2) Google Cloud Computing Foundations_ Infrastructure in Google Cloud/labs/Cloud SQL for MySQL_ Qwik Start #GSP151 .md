# GSP151
[![](https://api.pointscounter.me/servers/img/subscribe)](https://www.youtube.com/@CloudHustlers)
## Run in cloudshell
```cmd
export ZONE=
```
```cmd
export REGION=${ZONE::-2}
gcloud sql instances create myinstance \
--database-version=MYSQL_8_0 \
--tier=db-n1-standard-4 \
--region=$REGION
gcloud sql connect myinstance --user=root
```
### Now press `Enter`
```cmd
CREATE DATABASE guestbook;
```

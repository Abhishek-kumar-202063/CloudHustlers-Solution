# GSP104
[![](https://api.pointscounter.me/servers/img/subscribe)](https://www.youtube.com/@CloudHustlers)
## Run in cloudshell
```cmd
export REGION=
```
```cmd
gcloud config set dataproc/region $REGION
gcloud dataproc clusters create example-cluster --worker-boot-disk-size 500
gcloud dataproc jobs submit spark \
--cluster example-cluster \
--class org.apache.spark.examples.SparkPi \
--jars file:///usr/lib/spark/examples/jars/spark-examples.jar \
-- 1000
```

# GSP1164
## Run in cloudshell
```cmd
export ZONE=
```
```cmd
export REGION=${ZONE::-2}
gcloud pubsub topics create projects/$DEVSHELL_PROJECT_ID/topics/export-findings-pubsub-topic
gcloud pubsub subscriptions create export-findings-pubsub-topic-sub --topic=projects/$DEVSHELL_PROJECT_ID/topics/export-findings-pubsub-topic
```
> Search `Security Command Center` > Overview > Settings > Continuous Exports </br>
> Create Pub/Sub Export > name `export-findings-pubsub` </br>
> Project name `your gcp id` > Topic `export-findings-pubsub-topic` > Save </br>
```cmd
gcloud pubsub subscriptions update export-findings-pubsub-topic-sub --ack-deadline=10
gcloud compute instances create instance-1 --zone=$ZONE \
--machine-type e2-micro \
--scopes=https://www.googleapis.com/auth/cloud-platform
gcloud alpha pubsub subscriptions pull export-findings-pubsub-topic-sub --limit=10
PROJECT_ID=$(gcloud config get project)
bq --location=$REGION --apilog=/dev/null mk --dataset \
$PROJECT_ID:continuous_export_dataset
gcloud services enable securitycenter.googleapis.com
gcloud scc bqexports create scc-bq-cont-export --dataset=projects/$DEVSHELL_PROJECT_ID/datasets/continuous_export_dataset --project=$DEVSHELL_PROJECT_ID
for i in {0..2}; do
gcloud iam service-accounts create sccp-test-sa-$i;
gcloud iam service-accounts keys create /tmp/sa-key-$i.json \
--iam-account=sccp-test-sa-$i@$DEVSHELL_PROJECT_ID.iam.gserviceaccount.com;
done
bq query --apilog=/dev/null --use_legacy_sql=false  \
"SELECT finding_id,event_time,finding.category FROM continuous_export_dataset.findings"
bq_command="bq query --apilog=/dev/null --use_legacy_sql=false \
\"SELECT finding_id, event_time, finding.category FROM continuous_export_dataset.findings\""
while true; do
    output=$(eval $bq_command)
    if [ -n "$output" ]; then
        echo "$output"
        break
    else
        echo "SUBSCRIBE TO CLOUDHUSTLERS"
        sleep 10
    fi
done
echo -e "\033[1;34mscc-export-bucket-$DEVSHELL_PROJECT_ID\033[0m"
```
> Copy Bucket Name From Last line of terminal Blue in color

> Cloud storage > Create > name `paste the name` > continue

> Region > Your REGION > create > confirm

> Search `Security Command Center` > Overview > Findings

> export > cloud storage > Select the bucket > Inside the bucket set file name to `findings.jsonl`

>Project name `your gcp id`

>path `from the table in lab` > Format `JSONL` > Time Range `All Time` > EXPORT

> BigQuery > + ADD > Google cloud storage

> Select `findings.jsonl` file from the bucket

>path `from the table in lab` > File format `JSONL`

>Dataset `continuous_export_dataset` > Table `old_findings`

> Enable `Edit As Text`

```cmd
[   
  {
    "mode": "NULLABLE",
    "name": "resource",
    "type": "JSON"
  },   
  {
    "mode": "NULLABLE",
    "name": "finding",
    "type": "JSON"
  }
]
```

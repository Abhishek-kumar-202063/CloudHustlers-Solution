# ARC101
## Run in cloudshell
```cmd
export USERNAME2=
```
```cmd
export REGION=
```
```cmd
export BUCKET_NAME=
```
```cmd
export TOPIC_NAME=
```
```cmd
export FUNCTION_NAME=
```
```cmd
gcloud services enable \
  artifactregistry.googleapis.com \
  cloudfunctions.googleapis.com \
  cloudbuild.googleapis.com \
  eventarc.googleapis.com \
  run.googleapis.com \
  logging.googleapis.com \
  pubsub.googleapis.com
gsutil mb -l $REGION gs://$BUCKET_NAME
gcloud projects add-iam-policy-binding $DEVSHELL_PROJECT_ID \
--member=user:$USERNAME2 \
--role="roles/storage.objectViewer"
gcloud pubsub topics create $TOPIC_NAME
mkdir cloudhustlers
cd cloudhustlers
cat > index.js << EOF
/* globals exports, require */
//jshint strict: false
//jshint esversion: 6
"use strict";
const crc32 = require("fast-crc32c");
const { Storage } = require('@google-cloud/storage');
const gcs = new Storage();
const { PubSub } = require('@google-cloud/pubsub');
const imagemagick = require("imagemagick-stream");
exports.thumbnail = (event, context) => {
  const fileName = event.name;
  const bucketName = event.bucket;
  const size = "64x64"
  const bucket = gcs.bucket(bucketName);
  const topicName = "$TOPIC_NAME";
  const pubsub = new PubSub();
  if ( fileName.search("64x64_thumbnail") == -1 ){
    // doesn't have a thumbnail, get the filename extension
    var filename_split = fileName.split('.');
    var filename_ext = filename_split[filename_split.length - 1];
    var filename_without_ext = fileName.substring(0, fileName.length - filename_ext.length );
    if (filename_ext.toLowerCase() == 'png' || filename_ext.toLowerCase() == 'jpg'){
      // only support png and jpg at this point
      console.log("Processing Original: gs://"+bucketName+"/"+fileName);
      const gcsObject = bucket.file(fileName);
      let newFilename = filename_without_ext + size + '_thumbnail.' + filename_ext;
      let gcsNewObject = bucket.file(newFilename);
      let srcStream = gcsObject.createReadStream();
      let dstStream = gcsNewObject.createWriteStream();
      let resize = imagemagick().resize(size).quality(90);
      srcStream.pipe(resize).pipe(dstStream);
      return new Promise((resolve, reject) => {
        dstStream
          .on("error", (err) => {
            console.log("Error: "+err);
            reject(err);
          })
          .on("finish", () => {
            console.log("Success: "+fileName+" → "+newFilename);
              // set the content-type
              gcsNewObject.setMetadata(
              {
                contentType: 'image/'+ filename_ext.toLowerCase()
              }, function(err, apiResponse) {});
              pubsub
                .topic(topicName)
                .publisher()
                .publish(Buffer.from(newFilename))
                .then(messageId => {
                  console.log("Message "+messageId+" published.");
                })
                .catch(err => {
                  console.error('ERROR:', err);
                });
          });
      });
    }
    else {
      console.log("gs://"+bucketName+"/"+fileName+" is not an image I can handle");
    }
  }
  else {
    console.log("gs://"+bucketName+"/"+fileName+" already has a thumbnail");
  }
};
EOF
cat > package.json << EOF
{
  "name": "thumbnails",
  "version": "1.0.0",
  "description": "Create Thumbnail of uploaded image",
  "scripts": {
    "start": "node index.js"
  },
  "dependencies": {
    "@google-cloud/pubsub": "^2.0.0",
    "@google-cloud/storage": "^5.0.0",
    "fast-crc32c": "1.0.4",
    "imagemagick-stream": "4.1.1"
  },
  "devDependencies": {},
  "engines": {
    "node": ">=4.3.2"
  }
}
EOF
while true; do
  deployment_result=$(gcloud functions deploy $FUNCTION_NAME \
    --runtime=nodejs18 \
    --trigger-resource=$BUCKET_NAME \
    --trigger-event=google.storage.object.finalize \
    --entry-point=thumbnail \
    --region=$REGION 2>&1)
  if echo "$deployment_result" | grep -q "status: ACTIVE"; then
    echo "Cloud function deployed successfully Cloud Hustlers"
    break
  else
    echo "Retrying in 5 seconds..."
    sleep 5
  fi
done
curl -o travel.jpg https://storage.googleapis.com/cloud-training/arc101/travel.jpg
gsutil cp travel.jpg gs://$BUCKET_NAME/travel.jpg
```
> Search ```Create alerting policy``` > Select a metric 

> Click on Active

> Cloud Function > Function > Active Instances

> Next > Threshold value ```0``` > Next

> Notification Channel > Manage Notification Channel

> In `Email` > Add New

> In `Email Address` write `arc101@cloudhustlers.in`

> In `Display Name` write `CloudHustlers`

> Save > Close 

> Notification Channel > Refresh

> Select `CloudHustlers` > ok

> In Alter Policy Name ```Active Cloud Function Instances``` > Create Policy

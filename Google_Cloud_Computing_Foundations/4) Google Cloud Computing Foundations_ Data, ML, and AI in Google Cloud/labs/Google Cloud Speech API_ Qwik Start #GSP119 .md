# GSP119
[![](https://api.pointscounter.me/servers/img/subscribe)](https://www.youtube.com/@CloudHustlers)
## Run in cloudshell
### Press `y` , `ENTER` > `ENTER` > `ENTER` > `n`, `ENTER`
```cmd
gcloud compute ssh linux-instance
```
### Get API KEY from `Navigation menu` > `APIs & services` > `Credentials` > `Create credentials` > `API key`
```cmd
export API_KEY=
```
```cmd
cat > request.json <<EOF
{
  "config": {
      "encoding":"FLAC",
      "languageCode": "en-US"
  },
  "audio": {
      "uri":"gs://cloud-samples-tests/speech/brooklyn.flac"
  }
}
EOF
curl -s -X POST -H "Content-Type: application/json" --data-binary @request.json \
"https://speech.googleapis.com/v1/speech:recognize?key=${API_KEY}" > result.json
```

# GSP848
>🚨 [PLEASE SUBSCRIBE OUR CHANNEL CLOUDHUSTLER](https://www.youtube.com/@cloudhustlers) 
## Run in cloudshell
```cmd
bq mk soccer
bq load \
--source_format=NEWLINE_DELIMITED_JSON \
--autodetect \
$DEVSHELL_PROJECT_ID:soccer.competitions \
gs://spls/bq-soccer-analytics/competitions.json
bq load \
--source_format=NEWLINE_DELIMITED_JSON \
--autodetect \
$DEVSHELL_PROJECT_ID:soccer.matches \
gs://spls/bq-soccer-analytics/matches.json
bq load \
--source_format=NEWLINE_DELIMITED_JSON \
--autodetect \
$DEVSHELL_PROJECT_ID:soccer.teams \
gs://spls/bq-soccer-analytics/teams.json
bq load \
--source_format=NEWLINE_DELIMITED_JSON \
--autodetect \
$DEVSHELL_PROJECT_ID:soccer.players \
gs://spls/bq-soccer-analytics/players.json
bq load \
--source_format=NEWLINE_DELIMITED_JSON \
--autodetect \
$DEVSHELL_PROJECT_ID:soccer.events \
gs://spls/bq-soccer-analytics/events.json
bq load \
--source_format=CSV \
--autodetect \
$DEVSHELL_PROJECT_ID:soccer.tags2name \
gs://spls/bq-soccer-analytics/tags2name.csv
bq query --use_legacy_sql=false '
SELECT
  (firstName || " " || lastName) AS player,
  birthArea.name AS birthArea,
  height
FROM
  `soccer.players`
WHERE
  role.name = "Defender"
ORDER BY
  height DESC
LIMIT 5
'
bq query --use_legacy_sql=false '
SELECT
  eventId,
  eventName,
  COUNT(id) AS numEvents
FROM
  `soccer.events`
GROUP BY
  eventId, eventName
ORDER BY
  numEvents DESC
'
```

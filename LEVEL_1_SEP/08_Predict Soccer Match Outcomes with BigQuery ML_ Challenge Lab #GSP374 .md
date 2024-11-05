# GSP374
>🚨 [PLEASE SUBSCRIBE OUR CHANNEL CLOUDHUSTLER](https://www.youtube.com/@cloudhustlers) 
## Run in cloudshell
```cmd
Events_Table_Name_cloudhustler=
```
```cmd
Tags_Table_Name_cloudhustler=
```
```cmd
Value1=
```
```cmd
Value2=
```
```cmd
Value3=
```
```cmd
Value4=
```
```cmd
Value5=
```
```cmd
number=$(echo "$Events_Table_Name_cloudhustler" | tr -cd '0-9')
Model_Name=soccer.xg_logistic_reg_model_$number
TASK4_FUNCTION1=soccer.GetShotDistanceToGoal$number
TASK4_FUNCTION2=soccer.GetShotAngleToGoal$number
bq load \
--source_format=NEWLINE_DELIMITED_JSON \
--autodetect \
$DEVSHELL_PROJECT_ID:soccer.$Events_Table_Name_cloudhustler \
gs://spls/bq-soccer-analytics/events.json
bq load \
--source_format=CSV \
--autodetect \
$DEVSHELL_PROJECT_ID:soccer.$Tags_Table_Name_cloudhustler \
gs://spls/bq-soccer-analytics/tags2name.csv
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
bq query --use_legacy_sql=false '
SELECT
playerId,
(Players.firstName || " " || Players.lastName) AS playerName,
COUNT(id) AS numPKAtt,
SUM(IF(101 IN UNNEST(tags.id), 1, 0)) AS numPKGoals,
SAFE_DIVIDE(
SUM(IF(101 IN UNNEST(tags.id), 1, 0)),
COUNT(id)
) AS PKSuccessRate
FROM
`soccer.'$Events_Table_Name_cloudhustler'` Events
LEFT JOIN
`soccer.players` Players ON
Events.playerId = Players.wyId
WHERE
eventName = "Free Kick" AND
subEventName = "Penalty"
GROUP BY
playerId, playerName
HAVING
numPkAtt >= 5
ORDER BY
PKSuccessRate DESC, numPKAtt DESC
'
bq query --use_legacy_sql=false '
WITH
Shots AS
(
SELECT
*,
(101 IN UNNEST(tags.id)) AS isGoal,
SQRT(
POW(
  (100 - positions[ORDINAL(1)].x) * '$Value1/$Value2',
  2) +
POW(
  (60 - positions[ORDINAL(1)].y) * '$Value3/$Value4',
  2)
 ) AS shotDistance
FROM
`soccer.'$Events_Table_Name_cloudhustler'`
WHERE
eventName = "Shot" OR
(eventName = "Free Kick" AND subEventName IN ("Free kick shot", "Penalty"))
)
SELECT
ROUND(shotDistance, 0) AS ShotDistRound0,
COUNT(*) AS numShots,
SUM(IF(isGoal, 1, 0)) AS numGoals,
AVG(IF(isGoal, 1, 0)) AS goalPct
FROM
Shots
WHERE
shotDistance <= 50
GROUP BY
ShotDistRound0
ORDER BY
ShotDistRound0	
'
bq query --use_legacy_sql=false '
CREATE FUNCTION `'$TASK4_FUNCTION1'`(x INT64, y INT64)
RETURNS FLOAT64
AS (
 SQRT(
   POW(('$Value1' - x) * '$Value3/100', 2) +
   POW(('$Value2' - y) * '$Value4/100', 2)
   )
 );'
bq query --use_legacy_sql=false '
CREATE FUNCTION `'$TASK4_FUNCTION2'`(x INT64, y INT64)
RETURNS FLOAT64
AS (
 SAFE.ACOS(
   SAFE_DIVIDE(
     ( 
       (POW('$Value3' - (x * '$Value3'/100), 2) + POW('$Value5' + (7.32/2) - (y * '$Value4'/100), 2)) +
       (POW('$Value3' - (x * '$Value3'/100), 2) + POW('$Value5' - (7.32/2) - (y * '$Value4'/100), 2)) -
       /* Squared length of goal opening, in meters */
       POW(7.32, 2)
     ),
     (2 *
       SQRT(POW('$Value3' - (x * '$Value3'/100), 2) + POW('$Value5' + 7.32/2 - (y * '$Value4'/100), 2)) *
       SQRT(POW('$Value3' - (x * '$Value3'/100), 2) + POW('$Value5' - 7.32/2 - (y * '$Value4'/100), 2))
     )
    )
  /* Translate radians to degrees */
  ) * 180 / ACOS(-1)
 )
;'
bq query --use_legacy_sql=false '
CREATE MODEL `'$Model_Name'`
OPTIONS(
model_type = "LOGISTIC_REG",
input_label_cols = ["isGoal"]
) AS
SELECT
Events.subEventName AS shotType,
(101 IN UNNEST(Events.tags.id)) AS isGoal,
`'$TASK4_FUNCTION1'` (Events.positions[ORDINAL(1)].x,
Events.positions[ORDINAL(1)].y) AS shotDistance,
`'$TASK4_FUNCTION2'` (Events.positions[ORDINAL(1)].x,
Events.positions[ORDINAL(1)].y) AS shotAngle
FROM
`soccer.'$Events_Table_Name_cloudhustler'` Events
LEFT JOIN
`soccer.matches` Matches ON
Events.matchId = Matches.wyId
LEFT JOIN
`soccer.competitions` Competitions ON
Matches.competitionId = Competitions.wyId
WHERE
Competitions.name != "World Cup" AND
(
eventName = "Shot" OR
(eventName = "Free Kick" AND subEventName IN ("Free kick shot", "Penalty"))
)
;'
bq query --use_legacy_sql=false '
SELECT
predicted_isGoal_probs[ORDINAL(1)].prob AS predictedGoalProb,
* EXCEPT (predicted_isGoal, predicted_isGoal_probs),
FROM
ML.PREDICT(
MODEL `'$Model_Name'`, 
(
 SELECT
   Events.playerId,
   (Players.firstName || " " || Players.lastName) AS playerName,
   Teams.name AS teamName,
   CAST(Matches.dateutc AS DATE) AS matchDate,
   Matches.label AS match,
 /* Convert match period and event seconds to minute of match */
   CAST((CASE
     WHEN Events.matchPeriod = "1H" THEN 0
     WHEN Events.matchPeriod = "2H" THEN 45
     WHEN Events.matchPeriod = "E1" THEN 90
     WHEN Events.matchPeriod = "E2" THEN 105
     ELSE 120
     END) +
     CEILING(Events.eventSec / 60) AS INT64)
     AS matchMinute,
   Events.subEventName AS shotType,

   (101 IN UNNEST(Events.tags.id)) AS isGoal,
 
   `'$TASK4_FUNCTION1'`(Events.positions[ORDINAL(1)].x,
       Events.positions[ORDINAL(1)].y) AS shotDistance,
   `'$TASK4_FUNCTION2'`(Events.positions[ORDINAL(1)].x,
       Events.positions[ORDINAL(1)].y) AS shotAngle
 FROM
   `soccer.'$Events_Table_Name_cloudhustler'` Events
 LEFT JOIN
   `soccer.matches` Matches ON
       Events.matchId = Matches.wyId
 LEFT JOIN
   `soccer.competitions` Competitions ON
       Matches.competitionId = Competitions.wyId
 LEFT JOIN
   `soccer.players` Players ON
       Events.playerId = Players.wyId
 LEFT JOIN
   `soccer.teams` Teams ON
       Events.teamId = Teams.wyId
 WHERE
   /* Look only at World Cup matches to apply model */
   Competitions.name = "World Cup" AND

   (
     eventName = "Shot" OR
     (eventName = "Free Kick" AND subEventName IN ("Free kick shot"))
   ) AND
   /* Filter only to goals scored */
   (101 IN UNNEST(Events.tags.id))
)
)
ORDER BY
predictedgoalProb'
```

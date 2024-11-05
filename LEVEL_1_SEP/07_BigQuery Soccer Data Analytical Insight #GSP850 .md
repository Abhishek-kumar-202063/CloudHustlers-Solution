# GSP850
>🚨 [PLEASE SUBSCRIBE OUR CHANNEL CLOUDHUSTLER](https://www.youtube.com/@cloudhustlers) 
```cmd
bq query --use_legacy_sql=false '
SELECT
 Events.playerId,
 (Players.firstName || " " || Players.lastName) AS playerName,
 SUM(IF(Tags2Name.Label = "assist", 1, 0)) AS numAssists
FROM
 `soccer.events` Events,
 Events.tags Tags
LEFT JOIN
 `soccer.tags2name` Tags2Name ON
   Tags.id = Tags2Name.Tag
LEFT JOIN
 `soccer.players` Players ON
   Events.playerId = Players.wyId
GROUP BY
 playerId, playerName
ORDER BY
 numAssists DESC
'
bq query --use_legacy_sql=false '
WITH
Passes AS
(
 SELECT
   *,
   (1801 IN UNNEST(tags.id)) AS accuratePass,
   (CASE
     WHEN ARRAY_LENGTH(positions) != 2 THEN NULL
     ELSE
       SQRT(
         POW(
           (positions[ORDINAL(2)].x - positions[ORDINAL(1)].x) * 105/100,
           2) +
         POW(
           (positions[ORDINAL(2)].y - positions[ORDINAL(1)].y) * 68/100,
           2)
         )
     END) AS passDistance
 FROM
   `soccer.events`
 WHERE
   eventName = "Pass"
)
SELECT
 Passes.teamId,
 Teams.name AS team,
 Teams.area.name AS teamArea,
 COUNT(Passes.Id) AS numPasses,
 AVG(Passes.passDistance) AS avgPassDistance,
 SAFE_DIVIDE(
   SUM(IF(Passes.accuratePass, Passes.passDistance, 0)),
   SUM(IF(Passes.accuratePass, 1, 0))
   ) AS avgAccuratePassDistance
FROM
 Passes
LEFT JOIN
 `soccer.teams` Teams ON
   Passes.teamId = Teams.wyId
WHERE
 Teams.type = "club"
GROUP BY
 teamId, team, teamArea
ORDER BY
 avgPassDistance
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
      (100 - positions[ORDINAL(1)].x) * 105/100,
      2) +
    POW(
      (50 - positions[ORDINAL(1)].y) * 68/100,
      2)
     ) AS shotDistance
 FROM
  `soccer.events`
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
WITH
Shots AS
(
 SELECT
  *,
  (101 IN UNNEST(tags.id)) AS isGoal,
  LEAST(positions[ORDINAL(1)].x, 99.99999) * 105/100 AS shotXAbs,
  LEAST(positions[ORDINAL(1)].y, 99.99999) * 68/100 AS shotYAbs
 FROM
   `soccer.events`
 WHERE
   eventName = "Shot" OR
   (eventName = "Free Kick" AND subEventName IN ("Free kick shot", "Penalty"))
),
ShotsWithAngle AS
(
 SELECT
   Shots.*,
   SAFE.ACOS(
     SAFE_DIVIDE(
       ( 
         (POW(105 - shotXAbs, 2) + POW(34 + (7.32/2) - shotYAbs, 2)) +
         (POW(105 - shotXAbs, 2) + POW(34 - (7.32/2) - shotYAbs, 2)) -
         POW(7.32, 2)
       ),
       (2 *
         SQRT(POW(105 - shotXAbs, 2) + POW(34 + 7.32/2 - shotYAbs, 2)) *
         SQRT(POW(105 - shotXAbs, 2) + POW(34 - 7.32/2 - shotYAbs, 2))
       )
     )
   /* Translate radians to degrees */
   ) * 180 / ACOS(-1)
   AS shotAngle
 FROM
   Shots
)
SELECT
 ROUND(shotAngle, 0) AS ShotAngleRound0,
 COUNT(*) AS numShots,
 SUM(IF(isGoal, 1, 0)) AS numGoals,
 AVG(IF(isGoal, 1, 0)) AS goalPct
FROM
 ShotsWithAngle
GROUP BY
 ShotAngleRound0
ORDER BY
 ShotAngleRound0
'
```

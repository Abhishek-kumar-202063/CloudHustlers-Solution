# GSP1041
[![]()](https://www.youtube.com/@CloudHustlers)
```cmd
bq mk \
--use_legacy_sql=false \
--description "DESCRIPTION" \
--view 'SELECT * FROM `bigquery-public-data.geo_us_boundaries.zip_codes`
WHERE state_code="TX"
LIMIT 4000' \
--project_id $DEVSHELL_PROJECT_ID \
demo_dataset.authorized_view_a
bq mk \
--use_legacy_sql=false \
--description "DESCRIPTION" \
--view 'SELECT * FROM `bigquery-public-data.geo_us_boundaries.zip_codes`
WHERE state_code="CA"
LIMIT 4000' \
--project_id $DEVSHELL_PROJECT_ID \
demo_dataset.authorized_view_b
echo "PROJECT_ID=$DEVSHELL_PROJECT_ID"
```
### Copy the project id (From last line in terminal) and Store it
### Bigquery > demo_dataset 
> sharing > Authorize Views > ```demo_dataset``` > select a > add authorization

> Then again > ```demo_dataset``` > select b > add authorization > close
### authorized_view_a > Share
> Add Principal > Paste username A from lab > Role ```BigQuery Data Viewer``` > save
### authorized_view_b > Share
> Add Principal > Paste username B from lab > Role ```BigQuery Data Viewer``` > save
### Close the incognito window
### Login to Project A Console 
```cmd
PROJECT_ID=
```
```cmd
bq mk --use_legacy_sql=false --view 'SELECT * FROM `'$PROJECT_ID'.demo_dataset.authorized_view_a`' customer_a_dataset.customer_a_table
```
### Close the incognito window
### Login to Project B Console 
```cmd
PROJECT_ID=
```
```cmd
bq mk --use_legacy_sql=false --view 'SELECT * FROM `'$PROJECT_ID'.demo_dataset.authorized_view_b`' customer_b_dataset.customer_b_table
```

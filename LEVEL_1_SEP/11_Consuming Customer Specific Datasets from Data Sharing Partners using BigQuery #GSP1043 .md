# GSP1043
[![]()](https://www.youtube.com/@CloudHustlers)
### Run in cloudshell
```cmd
bq query \
--use_legacy_sql=false \
--destination_table=$DEVSHELL_PROJECT_ID:demo_dataset.authorized_table \
'SELECT * FROM (
SELECT *, ROW_NUMBER() OVER (PARTITION BY state_code ORDER BY area_land_meters DESC) AS cities_by_area
FROM `bigquery-public-data.geo_us_boundaries.zip_codes`) cities
WHERE cities_by_area <= 10 ORDER BY cities.state_code
LIMIT 1000;'
```
### Bigquery > demo_dataset
> Sharing > Authorize datasets > type and select ```demo_dataset``` > Add Authorization >close

### authorized_table > Share
> Add Principal > Paste username 1 and 2 from lab > Role ```BigQuery Data Viewer``` > save
### Close the incognito window
### Login to Publisher Console 
> For the project id copy it from task 1 step 6 (only projectid)
```cmd
PROJECT_ID_1=
```
```cmd
bq mk --use_legacy_sql=false --view 'SELECT *
FROM `'$PROJECT_ID_1'.demo_dataset.authorized_table`
WHERE state_code="NY"
LIMIT 1000' data_publisher_dataset.authorized_view
echo "PROJECT_ID_2=$DEVSHELL_PROJECT_ID"
```
### Copy the project id 2 (From last line in terminal) and Store it
### Bigquery > data_publisher_dataset
> Sharing > Authorize Views > type and select ```data_publisher_dataset``` > Add Authorization >close
### authorized_view > Share
>Add Principal > Paste username 2 from lab > Role ```BigQuery Data Viewer``` > save
### Close the incognito window
### Login to Customer (Data Twin) Console 
```cmd
PROJECT_ID_2=
```
```cmd
bq mk --use_legacy_sql=false --view 'SELECT cities.zip_code, cities.city, cities.state_code, customers.last_name, customers.first_name
FROM `'$DEVSHELL_PROJETC_ID'.customer_dataset.customer_info` as customers
JOIN `'$PROJECT_ID_2'.data_publisher_dataset.authorized_view` as cities
ON cities.state_code = customers.state;' customer_dataset.customer_table
```

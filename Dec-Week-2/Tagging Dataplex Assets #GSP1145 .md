# GSP1145
[![]()](https://www.youtube.com/@CloudHustlers)
### Run in CloudShell
```cmd
export REGION=
```
```cmd
gcloud services enable dataplex.googleapis.com
gcloud services enable datacatalog.googleapis.com
gcloud dataplex lakes create orders-lake \
   --location=$REGION \
   --display-name="Orders Lake"
gcloud dataplex zones create customer-curated-zone \
    --location=$REGION \
    --lake=orders-lake \
    --display-name="Customer Curated Zone" \
    --type=CURATED \
    --resource-location-type=SINGLE_REGION
gcloud dataplex assets create customer-details-dataset \
--location=$REGION \
--lake=orders-lake \
--zone=customer-curated-zone \
--display-name="Customer Details Dataset" \
--resource-type=BIGQUERY_DATASET \
--resource-name=projects/$DEVSHELL_PROJECT_ID/datasets/customers
```
### Search `Tag templates dataplex`
> open `Create tag template` in new tab > name `Protected Data Template` > Location `check in lab`

>Add Field >Name `Protected Data Flag` > Type `Enumerated` 

> Value 1 `YES` > ADD VALUE > VALUE 2 `NO` > Done > Create

### From left side click `Search`
> SEARCH `customer_details` > Attach Tags >
 
> check `zip` `state` `last_name` `country` `email` `latitude` `first_name` `city` `longitude`

>Choose the tag templates `Protected data template`

>Protected data flag `YES` > SAVE 

# GSP411
[![]()](https://www.youtube.com/@CloudHustlers)
### Run in Cloudshell
```cmd
bq mk ecommerce
gsutil cp gs://data-insights-course/exports/products.csv .
gsutil mb gs://$DEVSHELL_PROJECT_ID
wget https://github.com/siddharth7000/practice/raw/main/results-20231202-120235.xlsx
cloudshell download results-20231202-120235.xlsx
    bq load \
    --autodetect \
    --replace \
    --source_format=CSV \
    ecommerce.products \
    products.csv
bq load --autodetect \
  --source_format=CSV \
  $DEVSHELL_PROJECT_ID:ecommerce.products \
  gs://data-insights-course/exports/products.csv
bq query --use_legacy_sql=false '
#standardSQL
SELECT
  *,
  SAFE_DIVIDE(orderedQuantity,stockLevel) AS ratio
FROM
  ecommerce.products
WHERE
# include products that have been ordered and
# are 80% through their inventory
orderedQuantity > 0
AND SAFE_DIVIDE(orderedQuantity,stockLevel) >= .8
ORDER BY
  restockingLeadTime DESC
'
```
> [OPEN GOOGLE SHEET](https://docs.google.com/spreadsheets)

> file > import > upload > select the recently download file > Import Data

> Share > Name `cloudhustlers` > save > Copy Link
```cmd
LINK=
```
```cmd
bq mk \
--external_table_definition=@GOOGLE_SHEETS=$LINK \
ecommerce.products_comments
```

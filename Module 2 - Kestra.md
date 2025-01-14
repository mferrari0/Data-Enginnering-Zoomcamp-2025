## Module 2 Homework

### Assignment

### Quiz Questions

Complete the Quiz shown below. It’s a set of 6 multiple-choice questions to test your understanding of workflow orchestration, Kestra and ETL pipelines for data lakes and warehouses.

1) Within the execution for `Yellow` Taxi data for the year `2020` and month `12`: what is the uncompressed file size (i.e. the output file `yellow_tripdata_2020-12.csv` of the `extract` task)?
- 128.3 MB
- 134.5 MB
- 364.7 MB
- 692.6 MB

#### Answer: if I go to Outputs > extract > outputFiles > yellow_tripdata_2020-12.csv, I get this error message: {"message":"Not Found: /app/storage/zoomcamp/06-gcp-taxi/executions/5KqlIiEQihmY9PurOp33z1/tasks/extract/3KZuupgbuHRGZ9DNbdjGxa/5AcIEsLgk9PO4dyynsCJIq-yellow_tripdata_2020-12.csv (No such file or directory)","logref":null,"path":null,"_links":{"self":{"href":"/api/v1/executions/5KqlIiEQihmY9PurOp33z1/file?path=kestra:///zoomcamp/06-gcp-taxi/executions/5KqlIiEQihmY9PurOp33z1/tasks/extract/3KZuupgbuHRGZ9DNbdjGxa/5AcIEsLgk9PO4dyynsCJIq-yellow_tripdata_2020-12.csv","templated":false,"profile":null,"deprecation":null,"title":null,"hreflang":null,"type":null,"name":null}},"_embedded":{}}
so instead I did: 

wget https://github.com/DataTalksClub/nyc-tlc-data/releases/download/yellow/yellow_tripdata_2020-12.csv.gz

gunzip yellow_tripdata_2020-12.csv.gz

du -h yellow_tripdata_2020-12.csv

->128.3 MB

I had to improvise... the data is there as I checked in bigquery.





2) What is the value of the variable `file` when the inputs `taxi` is set to `green`, `year` is set to `2020`, and `month` is set to `04` during execution?
- `{{inputs.taxi}}_tripdata_{{inputs.year}}-{{inputs.month}}.csv` 
- `green_tripdata_2020-04.csv`
- `green_tripdata_04_2020.csv`
- `green_tripdata_2020.csv`

#### Answer:

executions > (find the last one) > details > labels > 	
file: green_tripdata_2020-04.csv





3) How many rows are there for the `Yellow` Taxi data for the year 2020?
- 13,537.299
- 24,648,499
- 18,324,219
- 29,430,127

#### Answer
SELECT COUNT(*) 
FROM `<xxx>.zoomcamp.yellow_tripdata` 
WHERE (TIMESTAMP_TRUNC(tpep_pickup_datetime, DAY) >= TIMESTAMP("2020-01-01")) AND (TIMESTAMP_TRUNC(tpep_pickup_datetime, DAY) <= TIMESTAMP("2020-12-31"))

> 24648647






4) How many rows are there for the `Green` Taxi data for the year 2020?
- 5,327,301
- 936,199
- 1,734,051
- 1,342,034

#### Answer
SELECT COUNT(*) 
FROM `<xxx>.zoomcamp.green_tripdata` 
WHERE (TIMESTAMP_TRUNC(lpep_pickup_datetime, DAY) >= TIMESTAMP("2020-01-01")) AND (TIMESTAMP_TRUNC(lpep_pickup_datetime, DAY) <= TIMESTAMP("2020-12-31"))


5) Using dbt on the `Green` and `Yellow` Taxi data for the year 2020, how many rows are there in the `fact_trips` table?
- 198
- 165
- 151
- 203


#### Answer:
I am not sure how to do that, because I don't know how to filter for 2020. I manually (in BQ) added  and EXTRACT(YEAR FROM lpep_pickup_datetime) = 2020 to the WHERE clause of the stg tables. I din't remove the LIMIT 100. and then I ran the same query
used to create the fact_trips table and obtained 196 rows. So I don't know what the correct answer is, but I have the feeling it is not the right way to proceed.

6) How would you configure the timezone to New York in a Schedule trigger?
- Add a `timezone` property set to `EST` in the `Schedule` trigger configuration  
- Add a `timezone` property set to `America/New_York` in the `Schedule` trigger configuration
- Add a `timezone` property set to `UTC-5` in the `Schedule` trigger configuration
- Add a `location` property set to `New_York` in the `Schedule` trigger configuration

#### Answer: as stated in https://kestra.io/docs/workflow-components/triggers/schedule-trigger, I should use:  timezone: America/New_York


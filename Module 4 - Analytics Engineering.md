## Module 4 Homework

### Question 1: 

**What happens when we execute dbt build --vars '{'is_test_run':'true'}'**
You'll need to have completed the ["Build the first dbt models"](https://www.youtube.com/watch?v=UVI30Vxzd6c) video. 
- It's the same as running *dbt build*
- It applies a _limit 100_ to all of our models
- It applies a _limit 100_ only to our staging models
- Nothing

Answer: It applies a _limit 100_ only to our staging models

### Question 2: 

**What is the code that our CI job will run? Where is this code coming from?**  

- The code that has been merged into the main branch
- The code that is behind the creation object on the dbt_cloud_pr_ schema
- The code from any development branch that has been opened based on main
- The code from the development branch we are requesting to merge to main


Answer: - The code from the development branch we are requesting to merge to main


### Question 3 (2 points)

**What is the count of records in the model fact_fhv_trips after running all dependencies with the test run variable disabled (:false)?**  
Create a staging model for the fhv data, similar to the ones made for yellow and green data. Add an additional filter for keeping only records with pickup time in year 2019.
Do not add a deduplication step. Run this models without limits (is_test_run: false).

Create a core model similar to fact trips, but selecting from stg_fhv_tripdata and joining with dim_zones.
Similar to what we've done in fact_trips, keep only records with known pickup and dropoff locations entries for pickup and dropoff locations. 
Run the dbt model without limits (is_test_run: false).

- 12998722
- 22998722
- 32998722
- 42998722


Answer:

this is the sql file for the staging model:

``` sql
  {{
      config(
          materialized='view'
      )
  }}
  
  
  WITH source_data AS (
      SELECT
          dispatching_base_num,
          pickup_datetime,
          dropOff_datetime,
          PUlocationID,
          DOlocationID,
          SR_Flag,
          Affiliated_base_number
      FROM {{ source('staging','fhv') }}
      WHERE EXTRACT(YEAR FROM pickup_datetime) = 2019
  )
  
  SELECT
      dispatching_base_num,
      pickup_datetime,
      dropOff_datetime,
      PUlocationID,
      DOlocationID,
      SR_Flag,
      Affiliated_base_number
  FROM source_data

```

and this is the core model:

``` sql

{{
    config(
        materialized='table'
    )
}}

WITH stg_fhv AS (
    SELECT *
    FROM {{ ref('stg_fhv') }}
),
dim_zones AS (
    SELECT *
    FROM {{ ref('dim_zones') }}
)

SELECT stg_fhv.*,
        pickup_zone.locationid AS pickup_locationid,
        pickup_zone.borough AS pickup_borough,
        pickup_zone.zone AS pickup_zone,
        dropoff_zone.locationid AS dropoff_locationid,
        dropoff_zone.borough AS dropoff_borough,
        dropoff_zone.zone AS dropoff_zone
FROM stg_fhv
INNER JOIN dim_zones AS pickup_zone
ON stg_fhv.PUlocationID = pickup_zone.locationid
INNER JOIN dim_zones AS dropoff_zone
ON stg_fhv.DOlocationID = dropoff_zone.locationid

```

the generated table has: 41,214,474 rows

### Question 4 (2 points)

**What is the service that had the most rides during the month of July 2019 month with the biggest amount of rides after building a tile for the fact_fhv_trips table and the fact_trips tile as seen in the videos?**

Create a dashboard with some tiles that you find interesting to explore the data. One tile should show the amount of trips per month, as done in the videos for fact_trips, including the fact_fhv_trips data.

- FHV
- Green
- Yellow
- FHV and Green

Answer: the plot shows Yellow caps reching more then 3M rides compared to the 2M from the FHV and the 0.4M for the green taxis





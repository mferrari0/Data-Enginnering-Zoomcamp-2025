## Module 3 Homework

### Assignment

### Quiz Questions

## Question 1:
Question 1: What is count of records for the 2022 Green Taxi Data??
- 65,623,481
- 840,402
- 1,936,423
- 253,647

Answer: 

SELECT
    COUNT(*) AS number_of_rows
FROM
    `steam-kingdom-44212.for_homework.green_2022`

Result: 840,402

## Question 2:
Write a query to count the distinct number of PULocationIDs for the entire dataset on both the tables.</br> 
What is the estimated amount of data that will be read when this query is executed on the External Table and the Table?

- 0 MB for the External Table and 6.41MB for the Materialized Table
- 18.82 MB for the External Table and 47.60 MB for the Materialized Table
- 0 MB for the External Table and 0MB for the Materialized Table
- 2.14 MB for the External Table and 0MB for the Materialized Table

Answer: SELECT COUNT(DISTINCT(PULocationID)) FROM `steam-kingdom-442121-u6.for_homework.green_2022` ;

the result is 0 MB for the External Table and 6.41MB for the Materialized Table


## Question 3:
How many records have a fare_amount of 0?
- 12,488
- 128,219
- 112
- 1,622

Answer: 
SELECT COUNT(*) 
FROM `steam-kingdom-442121-u6.for_homework.green_2022`
WHERE fare_amount = 0;

Result: 1,622


## Question 4:
What is the best strategy to make an optimized table in Big Query if your query will always order the results by PUlocationID and filter based on lpep_pickup_datetime? (Create a new table with this strategy)
- Cluster on lpep_pickup_datetime Partition by PUlocationID
- Partition by lpep_pickup_datetime  Cluster on PUlocationID
- Partition by lpep_pickup_datetime and Partition by PUlocationID
- Cluster on by lpep_pickup_datetime and Cluster on PUlocationID

Answer: I would partition by lpep_pickup_datetime and cluster by  PUlocationID:

-- Creating a partition and cluster table

CREATE OR REPLACE TABLE steam-kingdom-442121-u6.for_homework.green_partitioned_clustered_2022

PARTITION BY DATE(lpep_pickup_datetime)

CLUSTER BY PUlocationID AS

SELECT * FROM steam-kingdom-442121-u6.for_homework.green_2022;



## Question 5:
Write a query to retrieve the distinct PULocationID between lpep_pickup_datetime
06/01/2022 and 06/30/2022 (inclusive)</br>

Use the materialized table you created earlier in your from clause and note the estimated bytes. Now change the table in the from clause to the partitioned table you created for question 4 and note the estimated bytes processed. What are these values? </br>

Choose the answer which most closely matches.</br> 

- 22.82 MB for non-partitioned table and 647.87 MB for the partitioned table
- 12.82 MB for non-partitioned table and 1.12 MB for the partitioned table
- 5.63 MB for non-partitioned table and 0 MB for the partitioned table
- 10.31 MB for non-partitioned table and 10.31 MB for the partitioned table


Answer:

SELECT DISTINCT PULocationID

FROM `steam-kingdom-442121-u6.for_homework.green_2022`

WHERE lpep_pickup_datetime BETWEEN '2022-06-01' AND '2022-06-30';

result: 12.82MB


SELECT DISTINCT PULocationID

FROM `steam-kingdom-442121-u6.for_homework.green_partitioned_clustered_2022`

WHERE lpep_pickup_datetime BETWEEN '2022-06-01' AND '2022-06-30';

result: 1.12MB



## Question 6: 
Where is the data stored in the External Table you created?

- Big Query
- GCP Bucket
- Big Table
- Container Registry


Answer: GCP Bucket

## Question 7:
It is best practice in Big Query to always cluster your data:
- True
- False

Answer: False

## (Bonus: Not worth points) Question 8:
No Points: Write a `SELECT count(*)` query FROM the materialized table you created. How many bytes does it estimate will be read? Why?

Answer: 0B. Probably because this information is saved in BQ and doesn't need to be fetched.



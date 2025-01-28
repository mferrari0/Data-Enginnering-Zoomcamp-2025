### Question 1: 

**Install Spark and PySpark** 

- Install Spark
- Run PySpark
- Create a local spark session
- Execute spark.version.

What's the output?

Answer: '3.3.2'

### Question 2: 

**FHV October 2019**

Read the October 2019 FHV into a Spark Dataframe with a schema as we did in the lessons.

Repartition the Dataframe to 6 partitions and save it to parquet.

What is the average size of the Parquet (ending with .parquet extension) Files that were created (in MB)? Select the answer which most closely matches.

- 1MB
- 6MB
- 25MB
- 87MB

Answer: 
```
df = df.repartition(6)
df.write.parquet('fhv/2019/10/')
```
-> 6MB



### Question 3: 

**Count records** 

How many taxi trips were there on the 15th of October?

Consider only trips that started on the 15th of October.

- 108,164
- 12,856
- 452,470
- 62,610

Answer: 
```
# Filter for trips on October 15th, 2019 and count
trips_on_oct15 = df.filter(
    F.to_date(F.col("pickup_datetime")).isin("2019-10-15")
).count()

print(f"Number of taxi trips on October 15th, 2019: {trips_on_oct15}")
```

-> 62,610


### Question 4: 

**Longest trip for each day** 

What is the length of the longest trip in the dataset in hours?

- 631,152.50 Hours
- 243.44 Hours
- 7.68 Hours
- 3.32 Hours

Answer:
```
from pyspark.sql.functions import col, unix_timestamp, max as spark_max

# Calculate trip duration in hours
duration_df = df.withColumn(
    "trip_duration_hours",
    (
        unix_timestamp(col("dropoff_datetime")) - 
        unix_timestamp(col("pickup_datetime"))
    ) / 3600  # Convert seconds to hours
)

# Find the maximum trip duration
max_duration = duration_df.agg(spark_max("trip_duration_hours")).collect()[0][0]

print(f"Longest trip duration: {max_duration:.2f} hours")
```
-> Longest trip duration: 631152.50 hours



### Question 5: 

**User Interface**

Spark’s User Interface which shows the application's dashboard runs on which local port?

- 80
- 443
- 4040
- 8080

-> 4040


### Question 6: 

**Least frequent pickup location zone**

Load the zone lookup data into a temp view in Spark</br>
[Zone Data](https://github.com/DataTalksClub/nyc-tlc-data/releases/download/misc/taxi_zone_lookup.csv)

Using the zone lookup data and the FHV October 2019 data, what is the name of the LEAST frequent pickup location Zone?</br>

- East Chelsea
- Jamaica Bay
- Union Sq
- Crown Heights North


Answer: 
```
from pyspark.sql.functions import col, count

# Join on PULocationID
joined_df = df.join(
    df_zones,
    df.PULocationID == df_zones.LocationID,  # Match pickup location to zone ID
    "inner"
)

# Count trips per zone and find the least frequent
result = (joined_df
          .groupBy("Zone")  # Assuming zone name column is called "Zone"
          .agg(count("*").alias("trip_count"))
          .orderBy("trip_count")
          .limit(1)
         )

# Show result
result.show(truncate=False)
```
-> Jamayca Bay

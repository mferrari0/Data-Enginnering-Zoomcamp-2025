# Question 1. Knowing docker tags
If you don't remember docker commands, you can always use --help:

docker --help
You can do that for each subcommand like build and run:

docker build --help
Do the same for docker run.

Which subcommand does this?
Remove one or more images

- delete
- rc
- rmi
- rm

## Answer
- rmi (https://docs.docker.com/get-started/docker_cheatsheet.pdf)




# Question 2. Understanding docker first run
Run docker with the python:3.12.8 image in an interactive mode, use the entrypoint bash.



What's the version of pip in the image?

- 24.3.1
- 24.2.1
- 23.3.1
- 23.2.1

## Answer

I ran: 
$ docker run -it --entrypoint bash python:3.12.8

root@ce724b0c2824:/# pip --version

pip 24.3.1 from /usr/local/lib/python3.12/site-packages/pip (python 3.12)

so the answer is 24.3.1

# Prepare Postgres
Run Postgres and load data as shown in the videos We'll use the green taxi trips from October 2019:

wget https://github.com/DataTalksClub/nyc-tlc-data/releases/download/green/green_tripdata_2019-10.csv.gz
You will also need the dataset with zones:

wget https://github.com/DataTalksClub/nyc-tlc-data/releases/download/misc/taxi_zone_lookup.csv
Download this data and put it into Postgres.

You can use the code from the course. It's up to you whether you want to use Jupyter or a python script.

# Question 3. Count records
How many taxi trips were made on October 18th, 2019?

(Trips that started and finished on that day)

- 13417
- 15417
- 17417
- 19417


## Answer:
I ran:

SELECT *
FROM public.green_taxi_data
WHERE (lpep_pickup_datetime >= '2019-10-18 00:00:00') AND (lpep_dropoff_datetime < '2019-10-19 00:00:00');

and  the answer is 17417 

# Question 4. Longest trip for each day
Which was the pick up day with the longest trip distance? Use the pick up time for your calculations.

Tip: For every day, we only care about one single trip with the longest distance.

- 2019-10-11
- 2019-10-24
- 2019-10-26
- 2019-10-31

## Answer:
I ran:

SELECT *
FROM public.green_taxi_data
WHERE trip_distance = (SELECT MAX(trip_distance) FROM public.green_taxi_data)

and I got: "2019-10-31 23:23:41" as pickup time, so the answer is - 2019-10-31

# Question 5. Three biggest pickup zones
Which where the top pickup locations with over 13,000 in total_amount (across all trips) for 2019-10-18?

Consider only lpep_pickup_datetime when filtering by date.

- East Harlem North, East Harlem South, Morningside Heights
- East Harlem North, Morningside Heights
- Morningside Heights, Astoria Park, East Harlem South
- Bedford, East Harlem North, Astoria Park

## Answer:
I ran:

SELECT gtd."PULocationID", zones."Zone", SUM(gtd.total_amount) AS somma
FROM public.green_taxi_data AS gtd
JOIN public.zones AS zones ON zones."LocationID" = gtd."PULocationID"
WHERE gtd.lpep_pickup_datetime >= '2019-10-18 00:00:00' AND gtd.lpep_pickup_datetime < '2019-10-19 00:00:00'
GROUP BY gtd."PULocationID", zones."Zone"
HAVING SUM(gtd.total_amount) > 13000
ORDER BY somma DESC;

and the top three zones were: "East Harlem North", "East Harlem South", "Morningside Heights" 

# Question 6. Largest tip
For the passengers picked up in Ocrober 2019 in the zone name "East Harlem North" which was the drop off zone that had the largest tip?

Note: it's tip , not trip

We need the name of the zone, not the ID.

Yorkville West
JFK Airport
East Harlem North
East Harlem South

SELECT
    gtd."PULocationID",
    pu_zones."Zone" AS "PickupZone",
    gtd."DOLocationID",
    do_zones."Zone" AS "DropoffZone",
FROM
    public.green_taxi_data AS gtd
JOIN
    public.zones AS pu_zones ON pu_zones."LocationID" = gtd."PULocationID"
JOIN
    public.zones AS do_zones ON do_zones."LocationID" = gtd."DOLocationID"
WHERE
    gtd.lpep_pickup_datetime >= '2019-10-19 00:00:00' AND gtd.lpep_pickup_datetime < '2019-10-20 00:00:00' AND pu_zones."Zone" = 'East Harlem North'





#Terraform
In this section homework we'll prepare the environment by creating resources in GCP with Terraform.

In your VM on GCP/Laptop/GitHub Codespace install Terraform. Copy the files from the course repo here to your VM/Laptop/GitHub Codespace.

Modify the files as necessary to create a GCP Bucket and Big Query Dataset.

Question 7. Creating Resources
After updating the main.tf and variable.tf files run:

terraform apply
Paste the output of this command into the homework submission form.

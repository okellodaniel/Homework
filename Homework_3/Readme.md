# Homework Week 3

### Preparation
________________
1. External table creation
```
-- Create an External Table data
CREATE OR REPLACE EXTERNAL TABLE `data-talks-412218.data_talks_dani.green_taxi_data`
OPTIONS(
  format='PARQUET',
  uris=[
    'gs://data-talks-dani/green_taxi_data/green_tripdata_2022-*.parquet'
  ]
)
```
2. Non external table creation
```
-- Create normal table from External table

CREATE OR REPLACE TABLE data-talks-412218.data_talks_dani.green_taxi_data_non_external

AS SELECT * FROM data-talks-412218.data_talks_dani.green_taxi_data

```

## Questions and Answers
1. What is count of records for the 2022 Green Taxi Data??

    - 65,623,481
    + **840,402**
    - 1,936,423
    - 253,647

    Answer : **840,402**
    Approach
    ```
    SELECT COUNT (*) FROM `data-talks-412218.data_talks_dani.green_taxi_data`

    ```
2. Write a query to count the distinct number of PULocationIDs for the entire dataset on both the tables.
What is the estimated amount of data that will be read when this query is executed on the External Table and the Table?

    - 0 MB for the External Table and 6.41MB for the Materialized Table
    - 18.82 MB for the External Table and 47.60 MB for the Materialized Table
    - **0 MB for the External Table and 0MB for the Materialized Table**
    - 2.14 MB for the External Table and 0MB for the Materialized Table

    Answer : **0 MB for the External Table and 0MB for the Materialized Table**

    Approach
    - Table 1
    ```
    SELECT DISTINCT(PULocationID) 

    FROM data-talks-412218.data_talks_dani.green_taxi_data
    ```
    - Table 2
    ```
    SELECT DISTINCT(PULocationID) 

    FROM data-talks-412218.data_talks_dani.green_taxi_data_non_external
    ```
3. How many records have a fare_amount of 0?

    - 12,488
    - 128,219
    - 112
    - **1,622**
    Answer : **1,622**
    Approach
    ```
    SELECT COUNT (*) FROM `data-talks-412218.data_talks_dani.green_taxi_data`
    WHERE fare_amount = 0; 
    ```
4. What is the best strategy to make an optimized table in Big Query if your query will always order the results by PUlocationID and filter based on lpep_pickup_datetime? (Create a new table with this strategy)

    - Cluster on lpep_pickup_datetime Partition by PUlocationID
    - **Partition by lpep_pickup_datetime Cluster on PUlocationID**
    - Partition by lpep_pickup_datetime and Partition by PUlocationID
    - Cluster on by lpep_pickup_datetime and Cluster on PUlocationID
    Answer: **Partition by lpep_pickup_datetime Cluster on PUlocationID**

    Approach
    ```
    CREATE OR REPLACE TABLE data-talks-412218.data_talks_dani.green_taxi_data_partitioned_clustered
    PARTITION BY 
    DATE(lpep_pickup_datetime) 
    CLUSTER BY PULocationID
    AS 
    SELECT * FROM data-talks-412218.data_talks_dani.green_taxi_data_non_external
        
    ```

5. Write a query to retrieve the distinct PULocationID between lpep_pickup_datetime 06/01/2022 and 06/30/2022 (inclusive)Use the materialized table you created earlier in your from clause and note the estimated bytes. Now change the table in the from clause to the partitioned table you created for question 4 and note the estimated bytes processed. What are these values?Choose the answer which most closely matches.
    - 22.82 MB for non-partitioned table and 647.87 MB for the partitioned table
    - **12.82 MB for non-partitioned table and 1.12 MB for the partitioned table**
    - 5.63 MB for non-partitioned table and 0 MB for the partitioned table
    - 10.31 MB for non-partitioned table and 10.31 MB for the partitioned table
    
    Answer: **12.82 MB for non-partitioned table and 1.12 MB for the partitioned table**

    Approach:
    ```
    SELECT DISTINCT(PULocationID)
    FROM data-talks-412218.data_talks_dani.green_taxi_data_non_external 
    WHERE DATE(lpep_pickup_datetime) BETWEEN '2022-06-01' AND '2022-06-30'

    -- change database
    SELECT DISTINCT(PULocationID)
    FROM data-talks-412218.data_talks_dani.green_taxi_data_partitioned_clustered 
    WHERE DATE(lpep_pickup_datetime) BETWEEN '2022-06-01' AND '2022-06-30'
    ```
6. Where is the data stored in the External Table you created?

    - Big Query
    - **GCP Bucket**
    - Big Table
    - Container Registry

7. It is best practice in Big Query to always cluster your data:

    - **True**
    - False

---
id: 1kx9ofax0hsl4my4jvjm5a4
title: Time_travel
desc: ''
updated: 1670668793269
created: 1666560208238
---

## Using Time Travel
- Allows us to go back in time and restore data that was deleted accidentally
- Time Travel is subject to the retention time set for the particular object
  - Enterprise Edition: Up to 90 days

<details>
  <summary>Set up </summary>  

```sql
--Setting up table
CREATE OR REPLACE TABLE OUR_FIRST_DB.public.test (
   id int,
   first_name string,
  last_name string,
  email string,
  gender string,
  Job string,
  Phone string)
    
CREATE OR REPLACE FILE FORMAT MANAGE_DB.file_formats.csv_file
    type = csv
    field_delimiter = ','
    skip_header = 1
    
CREATE OR REPLACE STAGE MANAGE_DB.external_stages.time_travel_stage
    URL = 's3://data-snowflake-fundamentals/time-travel/'
    file_format = MANAGE_DB.file_formats.csv_file;
    

LIST @MANAGE_DB.external_stages.time_travel_stage

COPY INTO OUR_FIRST_DB.public.test
from @MANAGE_DB.external_stages.time_travel_stage
files = ('customers.csv')

SELECT * FROM OUR_FIRST_DB.public.test

```
</details>
<br>




```sql

2021-04-17 08:16:24.259

-- Setting up UTC time for convenience
ALTER SESSION SET TIMEZONE ='UTC'
SELECT DATEADD(DAY, 1, CURRENT_TIMESTAMP)

SELECT * FROM OUR_FIRST_DB.public.test before (timestamp => '2021-04-16 07:30:47.145'::timestamp)

```
### Using Time Travel Feature
#### Method 1: Time Travel by `OFFSET` minutes
```sql
------Using time travel: Method 1 - 2 minutes back
SELECT * FROM OUR_FIRST_DB.public.test at (OFFSET => -60*1.5)
```
#### Method 2: Time Travel by timestamp
```sql
------Using time travel: Method 2 - before timestamp
SELECT * FROM OUR_FIRST_DB.public.test before (timestamp => '2021-04-15 17:47:50.581'::timestamp)
```
#### Method 3: Time Travel by Query ID
```sql
------Using time travel: Method 3 - before Query ID
SELECT * FROM OUR_FIRST_DB.public.test before (statement => '019b9ee5-0500-8473-0043-4d8300073062')
```
### Restoring Data
<details>
  <summary>Set Up</summary>

```sql
--Setting up table

CREATE OR REPLACE TABLE OUR_FIRST_DB.public.test (
   id int,
   first_name string,
  last_name string,
  email string,
  gender string,
  Job string,
  Phone string);
    

COPY INTO OUR_FIRST_DB.public.test
from @MANAGE_DB.external_stages.time_travel_stage
files = ('customers.csv');

SELECT * FROM OUR_FIRST_DB.public.test;

--Use-case: Update data (by mistake)
UPDATE OUR_FIRST_DB.public.test
SET LAST_NAME = 'Tyson';


UPDATE OUR_FIRST_DB.public.test
SET JOB = 'Data Analyst';

SELECT * FROM OUR_FIRST_DB.public.test before (statement => '019b9eea-0500-845a-0043-4d830007402a')
```
</details>

<br>

#### Bad Method

```sql
------Bad method
CREATE OR REPLACE TABLE OUR_FIRST_DB.public.test as
SELECT * FROM OUR_FIRST_DB.public.test before (statement => '019b9eea-0500-845a-0043-4d830007402a')

SELECT * FROM OUR_FIRST_DB.public.test

CREATE OR REPLACE TABLE OUR_FIRST_DB.public.test as
SELECT * FROM OUR_FIRST_DB.public.test before (statement => '019b9eea-0500-8473-0043-4d830007307a') -- Time Travel Data No Longer Available
```

**Note**: DO NOT REPLACE THE TABLE!! 
- When we replace a table, we lose the time travel capability for the replaced table since the id associated to that object has also been replaced

<br>

#### Good Method
- Create another table with the restored data
- Truncate the main table and insert the restored data into it
```sql
------Good method
CREATE OR REPLACE TABLE OUR_FIRST_DB.public.test_backup as
SELECT * FROM OUR_FIRST_DB.public.test before (statement => '019b9ef0-0500-8473-0043-4d830007309a')

TRUNCATE OUR_FIRST_DB.public.test

INSERT INTO OUR_FIRST_DB.public.test
SELECT * FROM OUR_FIRST_DB.public.test_backup

SELECT * FROM OUR_FIRST_DB.public.test 
```

### The `UNDROP` Command
```sql
--UNDROP command - Tables
UNDROP TABLE OUR_FIRST_DB.public.customers;

--UNDROP command - Schemas
UNDROP SCHEMA OUR_FIRST_DB.public;

--UNDROP command - Database
UNDROP DATABASE OUR_FIRST_DB;
```
#### Remedy to the bad method of restoring data

```sql
--Restore replaced table 
UPDATE OUR_FIRST_DB.public.customers
SET LAST_NAME = 'Tyson';

UPDATE OUR_FIRST_DB.public.customers
SET JOB = 'Data Analyst';

--Undroping a with a name that already exists
CREATE OR REPLACE TABLE OUR_FIRST_DB.public.customers as
SELECT * FROM OUR_FIRST_DB.public.customers before (statement => '019b9f7c-0500-851b-0043-4d83000762be')

SELECT * FROM OUR_FIRST_DB.public.customers

UNDROP table OUR_FIRST_DB.public.customers;

ALTER TABLE OUR_FIRST_DB.public.customers
RENAME TO OUR_FIRST_DB.public.customers_wrong;


DESC table OUR_FIRST_DB.public.customers
    
```
### Retention Time


### Time Travel Cost
```sql

SELECT * FROM SNOWFLAKE.ACCOUNT_USAGE.STORAGE_USAGE ORDER BY USAGE_DATE DESC;

SELECT * FROM SNOWFLAKE.ACCOUNT_USAGE.TABLE_STORAGE_METRICS;

--Query time travel storage
SELECT 	ID, 
		TABLE_NAME, 
		TABLE_SCHEMA,
        TABLE_CATALOG,
		ACTIVE_BYTES / (1024*1024*1024) AS STORAGE_USED_GB,
		TIME_TRAVEL_BYTES / (1024*1024*1024) AS TIME_TRAVEL_STORAGE_USED_GB
FROM SNOWFLAKE.ACCOUNT_USAGE.TABLE_STORAGE_METRICS
ORDER BY STORAGE_USED_GB DESC,TIME_TRAVEL_STORAGE_USED_GB DESC;
```
---
id: 1d7i2bw4ln6rm3b9cjm30mn
title: Performance_optimization
desc: ''
updated: 1666563439441
created: 1666453109686
---


### Performance in Snowflake
#### Our job?
- Assigning appropriate data types
- Sizing Virtual warehouses correctly
- Cluster Keys

#### How?
- Dedicated Warehouses: Different groups have different needs, workloads
- Scaling Up: Known patterns of high workloads e.g. Batch Loading at night
- Scaling Out: Dynamically scaling for unknown patterns of work loads
- Maximize Cache Usage
  
### Create dedicated virtual warehouse
- Careful not to create too many warehouse
  - Warehouses are not fully used all the time
  - They are started and suspend
  - A lot of time that the warehouse is active, it's not being used
  - 
### Implement dedicated virtual warehouse
Note that you have the appropriate rights i.e. ACCOUNTADMIN


Suppose we create different warehouses for data scientist & DBA
- For Data Scientists
```sql
-- Step 1: Create warehouse with appropriate size
CREATE WAREHOUSE DS_WH 
WITH WAREHOUSE_SIZE = 'SMALL'
WAREHOUSE_TYPE = 'STANDARD' 
AUTO_SUSPEND = 300 
AUTO_RESUME = TRUE 
MIN_CLUSTER_COUNT = 1 
MAX_CLUSTER_COUNT = 1 
SCALING_POLICY = 'STANDARD';

-- Step 2: Create role for user groups
CREATE ROLE DATA_SCIENTIST;

-- Step 3: Grant usage to roles
GRANT USAGE ON WAREHOUSE DS_WH TO ROLE DATA_SCIENTIST;

-- Step 4: Set up user accounts
CREATE USER DS1 PASSWORD = 'DS1' LOGIN_NAME = 'DS1' DEFAULT_ROLE='DATA_SCIENTIST' DEFAULT_WAREHOUSE = 'DS_WH'  MUST_CHANGE_PASSWORD = FALSE;
CREATE USER DS2 PASSWORD = 'DS2' LOGIN_NAME = 'DS2' DEFAULT_ROLE='DATA_SCIENTIST' DEFAULT_WAREHOUSE = 'DS_WH'  MUST_CHANGE_PASSWORD = FALSE;
CREATE USER DS3 PASSWORD = 'DS3' LOGIN_NAME = 'DS3' DEFAULT_ROLE='DATA_SCIENTIST' DEFAULT_WAREHOUSE = 'DS_WH'  MUST_CHANGE_PASSWORD = FALSE;

-- Step 5: Grant Role to users
GRANT ROLE DATA_SCIENTIST TO USER DS1;
GRANT ROLE DATA_SCIENTIST TO USER DS2;
GRANT ROLE DATA_SCIENTIST TO USER DS3;
```
<details>
    <summary>For DBA</summary>
  
```SQL
CREATE WAREHOUSE DBA_WH 
WITH WAREHOUSE_SIZE = 'XSMALL'
WAREHOUSE_TYPE = 'STANDARD' 
AUTO_SUSPEND = 300 
AUTO_RESUME = TRUE 
MIN_CLUSTER_COUNT = 1 
MAX_CLUSTER_COUNT = 1 
SCALING_POLICY = 'STANDARD';

CREATE ROLE DBA;
GRANT USAGE ON WAREHOUSE DBA_WH TO ROLE DBA;

--Setting up users with roles
CREATE USER DBA1 PASSWORD = 'DBA1' LOGIN_NAME = 'DBA1' DEFAULT_ROLE='DBA' DEFAULT_WAREHOUSE = 'DBA_WH'  MUST_CHANGE_PASSWORD = FALSE;
CREATE USER DBA2 PASSWORD = 'DBA2' LOGIN_NAME = 'DBA2' DEFAULT_ROLE='DBA' DEFAULT_WAREHOUSE = 'DBA_WH'  MUST_CHANGE_PASSWORD = FALSE;

GRANT ROLE DBA TO USER DBA1;
GRANT ROLE DBA TO USER DBA2;

--Drop objects again
DROP USER DBA1;
DROP USER DBA2;

DROP USER DS1;
DROP USER DS2;
DROP USER DS3;

DROP ROLE DBA;
DROP WAREHOUSE DBA_WH;
```
</details>
<br>

### Scaling Up 
- Changing size of the warehouses for increased workloads at certain times
- Ideal for increasing performance when having complex queries
### Scaling Out
- Ideal for having more users concurrently
- Here, the approach is to add additional warehouses or to use multi-cluster warehouses
- Multi-cluster warehouses help to dynamically increase warehouses automatically for. e.g fluctuating number of users at different time periods
- From Enterprise Edition onwards, warehouses are of multi-cluster type (min: 1, max: can be very high)

### Caching
- Happens automatically
- if query executed twice, results are cached and can be re-used
- Results are cached for 24 hours or until underlaying data has changed
- To optimize, ensure similar queries go on the same warehouse

### Clustering
### Clustering in Practice

```sql
--Publicly accessible staging area    
CREATE OR REPLACE STAGE MANAGE_DB.external_stages.aws_stage
    url='s3://bucketsnowflakes3';

--List files in stage
LIST @MANAGE_DB.external_stages.aws_stage;

--Load data using copy command
COPY INTO OUR_FIRST_DB.PUBLIC.ORDERS
    FROM @MANAGE_DB.external_stages.aws_stage
    file_format= (type = csv field_delimiter=',' skip_header=1)
    pattern='.*OrderDetails.*';
    

--Create table
CREATE OR REPLACE TABLE ORDERS_CACHING (
ORDER_ID	VARCHAR(30)
,AMOUNT	NUMBER(38,0)
,PROFIT	NUMBER(38,0)
,QUANTITY	NUMBER(38,0)
,CATEGORY	VARCHAR(30)
,SUBCATEGORY	VARCHAR(30)
,DATE DATE)    

INSERT INTO ORDERS_CACHING 
SELECT
t1.ORDER_ID
,t1.AMOUNT	
,t1.PROFIT	
,t1.QUANTITY	
,t1.CATEGORY	
,t1.SUBCATEGORY	
,DATE(UNIFORM(1500000000,1700000000,(RANDOM())))
FROM ORDERS t1
CROSS JOIN (SELECT * FROM ORDERS) t2
CROSS JOIN (SELECT TOP 100 * FROM ORDERS) t3


--Query Performance before Cluster Key
SELECT * FROM ORDERS_CACHING  WHERE DATE = '2020-06-09'

--Adding Cluster Key & Compare the result
ALTER TABLE ORDERS_CACHING CLUSTER BY ( DATE ) 

SELECT * FROM ORDERS_CACHING  WHERE DATE = '2020-01-05'

--Not ideal clustering & adding a different Cluster Key using function
SELECT * FROM ORDERS_CACHING  WHERE MONTH(DATE)=11
ALTER TABLE ORDERS_CACHING CLUSTER BY ( MONTH(DATE) )

```
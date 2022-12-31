---
id: 02iif1rjxgq715lvsgg49km
title: streams
desc: ''
updated: 1670679828033
created: 1665969961098
---

# Streams #
- Objects that records/tracks/captures (DML-)changes made to a table i.e. `INSERT`, `UPDATE`, `DELETE`
- This process is called change data capture, CDC
- Has all of the columns of the tracked table + 3 additional columns: `METADATA$ACTION`, `METADATA$UPDATE`, `METADATA$ROW_ID`
- Data in the STREAM objects are just references to the data in the table, hence not incurring costs except for storing the extra metadata
- Once the data changes is captured in the STREAM object, we want to insert into the target table
- Once the target table is updated, the data will be removed in the STREAM object
- works like a view that tracks work that has to be done
- We can consume a stream using either `INSERT` or `CREATE TABLE` statement

## How to create streams ##
<br/>

```SQL
-- Creating stream
CREATE STREAM <stream name>
    ON TABLE <table name>

-- Streams can be queried
SELECT * FROM <stream name>    
```

### **`INSERT`** operation ## 

<br/>

```sql
-- Create stream object
CREATE OR REPLACE STREAM sales_stream ON TABLE sales_raw_staging

-- Get list of streams
SHOW STREAMS;

DESC STREAMS sales_stream;

-- Get data changes using stream
SELECT * FROM sales_stream;

-- Suppose new rows got inserted into sales_raw_staging, we will also see the new rows in the stream object
-- Consume stream object to update table; data in stream will be removed
INSERT INTO sales_final_table
    SELECT
        SA.ID,
        SA.PRODUCT,
        SA.PRICE,
        SA.AMOUNT,
        ST.STORE_ID,
        ST.LOCATION,
        ST.EMPLOYEE
    FROM sales_stream SA
    JOIN STORE_TABLE ST ON ST_STORE_ID=SA.STORE_ID

-- stream now is empty
SELECT * FROM sales_stream;

```

### **`UPDATE`** Operation ##

```sql
-- Suppose some value in the table got updated
-- For the UPDATE opration, we get a pair of records where METADATA$ACTION=DELETE and METADATA$ACTION=INSERT
UPDATE sales_Raw_Staging
SET PRODUCT = 'Potato' WHERE PRODUCT = 'Banana'

-- consume stream for merge command
MERGE INTO sales_final_table F
USING sales_stream S 
    ON f.id = s.id
WHEN MATCHED
    AND S.METADATA$ACTION = 'INSERT'
    AND S.METADATA$ISUPDATE = 'TRUE' -- indicate if records was updated
THEN UPDATE
SET 
    f.product = s.product,
    f.price = s.amount,
    f.store_id = s.store_id;

-- stream now is empty
SELECT * FROM sales_stream;
```
### **`DELETE`** Operation ##

```sql
-- Suppose some rows in the table got deleted
-- For the DELETE opration, we get one record where METADATA$ACTION=DELETE, METADATA$ISUPDATE=FAlSE
DELETE FROM sales_raw_staging
WHERE PRODUCT = 'Lemon';

-- consume stream for merge command
MERGE INTO sales_final_table F
USING sales_stream S 
    ON f.id = s.id
WHEN MATCHED
    AND S.METADATA$ACTION = 'DELETE'
    AND S.METADATA$ISUPDATE = 'FALSE' -- indicate if records was updated
THEN DELETE;

-- stream now is empty
SELECT * FROM sales_stream;
```

## Process All Data Changes ## 

```sql

INSERT INTO sales_raw_staging values(2, 'Lemon', 0.99, 1,1);
UPDATE sales_raw_staging
GET PRICE = 3 WHERE PRODUCT = 'Mango';
DELETE FROM sales_raw_staging WHERE product = 'Potato';

-- comsuming stream object
MERGE INTO sales_final_table F
USING (
        SELECT
            STRE.*, ST.location, ST.employees
        FROM sales_stream STRE
        JOIN store_table ST
            ON stre.store_id = ST.store_id
) S
    ON F.id = S.id
WHEN MATCHED
    AND S.METADATA$ACTION = 'DELETE'
    AND S.METADATA$ISUPDATE = 'FALSE'
    THEN DELETE
WHEN MATCHED
    AND S.METADATA$ACTION = 'INSERT'
    AND S.METADATA$ISUPDATE = 'TRUE' -- indicate if records was updated
THEN UPDATE
SET 
    f.product = s.product,
    f.price = s.price,
    f.amount = s.amount,
    f.store_id = s.store_id
WHEN NOT MATCHED
    AND METADATA$ACTION = 'INSERT'
    THEN INSERT(id, product, price, store_id, amount, employees, location)
    VALUES(S.id, S.product, S.price, S.store_id, S.amount, S.employees, S.location)    ;
```

## Combine Streams & tasks ##

```sql
-- Automate the updates using task
CREATE OR REPLACE TASK all_data_changes
    WAREHOSUE = COMPUTE_WH
    SCHEDULE = '1 MINUTE'
    WHEN SYSTEM$STREAM_HAS_DATA('SALES_STREAM')
    AS
MERGE INTO sales_final_table F
USING (
        SELECT
            STRE.*, ST.location, ST.employees
        FROM sales_stream STRE
        JOIN store_table ST
            ON stre.store_id = ST.store_id
) S
    ON F.id = S.id
WHEN MATCHED
    AND S.METADATA$ACTION = 'DELETE'
    AND S.METADATA$ISUPDATE = 'FALSE'
    THEN DELETE
WHEN MATCHED
    AND S.METADATA$ACTION = 'INSERT'
    AND S.METADATA$ISUPDATE = 'TRUE' -- indicate if records was updated
THEN UPDATE
SET 
    f.product = s.product,
    f.price = s.price,
    f.amount = s.amount,
    f.store_id = s.store_id
WHEN NOT MATCHED
    AND METADATA$ACTION = 'INSERT'
    THEN INSERT(id, product, price, store_id, amount, employees, location)
    VALUES(S.id, S.product, S.price, S.store_id, S.amount, S.employees, S.location)    ;

-- resume task
ALTER TASK all_data_changes RESUME;
SHOW TASKS;

-- verify the history
SELECT *
FROM TABLE(INFORMATION_SCHEMA.TASK_HISTORY())
ORDER BY NAME ASC, SCHEDULED_TIME DESC;
```

## Types of streams ##
- There are two types of streams, standard/default and append-only
    - STANDARD: `INSERT`, `UPDATE`, `DELETE` (incl `TRUNCATE`)
    - APPEND_ONLY: `INSERT`
    - INSERT_ONLY: For external tables only


```sql
-- create append-only stream
CREATE STREAM <stream name>
    ON TABLE <table name>
    APPEND_ONLY = TRUE
```

## Alternative to streams: `CHANGES` clause ## 

```sql
ALTER TABLE sales_raw
SET CHANGE_TRACKING = TRUE;

-- To see the changes, we can use the CHANGES clause
SELECT * FROM sales_raw
CHANGES(INFORMATION=> DEFAULT) -- Options same as streams: DEFAULT or APPEND-ONLY
AT (OFFSET => -0.5*60) -- note we are using the time travel feature

SELECT CURRENT_TIMESTAMP

-- Note: we only can see the latest change history per row
SELECT * FROM sales_raw
CHANGES(INFORMATION=> DEFAULT)
AT (TIMESTAMP => _____::TIMESTAMP_TZ) -- note we are using the time travel feature

-- we can create a table from the CHANGES clause, consuming won't result in an empty table unlike STREAMS
CREATE OR REPLACE TABLE PRODUCTS
AS
SELECT * FROM SALES_RAW
CHANGES(INFORMATION => APPEND_ONLY)
AT (TIMESTAMP => _____::TIMESTAMP_TZ) 

SELECT * FROM PRODUCTS

```
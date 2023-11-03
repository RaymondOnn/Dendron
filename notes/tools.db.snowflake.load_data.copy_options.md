---
id: kaypip9zyatezn3aejx8zzf
title: Copy_options
desc: ''
updated: 1666459540687
created: 1666447364674
---

### `VALIDATION_MODE`
- `VALIDATION_MODE = RETURN_n_ROWS | RETURN_ERRORS | RETURN_ALL_ERRORS`
-  the COPY command tests the files for errors but does not load them. 
-  The command validates the data to be loaded and returns results based on the validation option specified:

<br>

| Supported Values                        | Notes                                                                                                                                                                                                                    |
| --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `RETURN_n_ROWS` (e.g. `RETURN_10_ROWS)` | Validates the specified number of rows, if no errors are encountered; otherwise, fails at the first error encountered in the rows.                                                                                       |
| `RETURN_ERRORS`                         | Returns all errors (parsing, conversion, etc.) across all files specified in the COPY statement.                                                                                                                         |
| `RETURN_ALL_ERRORS`                     | Returns all errors across all files specified in the COPY statement, including files with errors that were partially loaded during an earlier load because the ON_ERROR copy option was set to CONTINUE during the load. |

<br>

<details>
    <summary>Set Up</summary>

```sql
--Prepare database & table
CREATE OR REPLACE DATABASE COPY_DB;

CREATE OR REPLACE TABLE  COPY_DB.PUBLIC.ORDERS (
    ORDER_ID VARCHAR(30),
    AMOUNT VARCHAR(30),
    PROFIT INT,
    QUANTITY INT,
    CATEGORY VARCHAR(30),
    SUBCATEGORY VARCHAR(30));

--Prepare stage object
CREATE OR REPLACE STAGE COPY_DB.PUBLIC.aws_stage_copy
    url='s3:--snowflakebucket-copyoption/size/';
  
LIST @COPY_DB.PUBLIC.aws_stage_copy;
```
</details>
<br>


```sql    
 --Load data using copy command
COPY INTO COPY_DB.PUBLIC.ORDERS
    FROM @aws_stage_copy
    file_format= (type = csv field_delimiter=',' skip_header=1)
    pattern='.*Order.*'
    VALIDATION_MODE = RETURN_ERRORS
    
COPY INTO COPY_DB.PUBLIC.ORDERS
    FROM @aws_stage_copy
    file_format= (type = csv field_delimiter=',' skip_header=1)
    pattern='.*Order.*'
   VALIDATION_MODE = RETURN_5_ROWS 
   
```
<details>
    <summary><h3 style="display:inline-block">Working with Rejected Records i.e. Records that failed validation</h3></summary>

```sql
   
---- Use files with errors ----
CREATE OR REPLACE STAGE COPY_DB.PUBLIC.aws_stage_copy
    url='s3:--snowflakebucket-copyoption/returnfailed/';

LIST @COPY_DB.PUBLIC.aws_stage_copy;    

COPY INTO COPY_DB.PUBLIC.ORDERS
    FROM @aws_stage_copy
    file_format= (type = csv field_delimiter=',' skip_header=1)
    pattern='.*Order.*'
    VALIDATION_MODE = RETURN_ERRORS

COPY INTO COPY_DB.PUBLIC.ORDERS
    FROM @aws_stage_copy
    file_format= (type = csv field_delimiter=',' skip_header=1)
    pattern='.*Order.*'
    VALIDATION_MODE = RETURN_1_rows
```
#### Working with error results
- Saving rejected files after VALIDATION_MODE
```sql
    CREATE OR REPLACE TABLE  COPY_DB.PUBLIC.ORDERS (
        ORDER_ID VARCHAR(30),
        AMOUNT VARCHAR(30),
        PROFIT INT,
        QUANTITY INT,
        CATEGORY VARCHAR(30),
        SUBCATEGORY VARCHAR(30));

    COPY INTO COPY_DB.PUBLIC.ORDERS
        FROM @aws_stage_copy
        file_format= (type = csv field_delimiter=',' skip_header=1)
        pattern='.*Order.*'
        VALIDATION_MODE = RETURN_ERRORS;

    --Storing rejected /failed results in a table
    CREATE OR REPLACE TABLE rejected AS 
    select rejected_record from table(result_scan(last_query_id()));

    INSERT INTO rejected
    select rejected_record from table(result_scan(last_query_id()));

    SELECT * FROM rejected;
```

</details>


---
<br>

### `SIZE_LIMIT`
- `SIZE_LIMIT = <num>`
- Specifies the maximum size (in bytes) of data to be loaded for a given COPY statement. When the threshold is exceeded, the COPY operation discontinues loading files.
<details>
    <summary>Set Up</summary>

```sql

--Prepare database & table
CREATE OR REPLACE DATABASE COPY_DB;
CREATE OR REPLACE TABLE  COPY_DB.PUBLIC.ORDERS (
    ORDER_ID VARCHAR(30),
    AMOUNT VARCHAR(30),
    PROFIT INT,
    QUANTITY INT,
    CATEGORY VARCHAR(30),
    SUBCATEGORY VARCHAR(30));
    
--Prepare stage object
CREATE OR REPLACE STAGE COPY_DB.PUBLIC.aws_stage_copy
    url='s3:--snowflakebucket-copyoption/size/';
    
--List files in stage
LIST @aws_stage_copy;

```
</details>
<br>

```sql
--Load data using copy command
COPY INTO COPY_DB.PUBLIC.ORDERS
    FROM @aws_stage_copy
    file_format= (type = csv field_delimiter=',' skip_header=1)
    pattern='.*Order.*'
    SIZE_LIMIT=20000;
```
---
<br>

### `RETURN_FAILED_ONLY`

<details>
    <summary>Set Up</summary>

```sql
CREATE OR REPLACE TABLE  COPY_DB.PUBLIC.ORDERS (
    ORDER_ID VARCHAR(30),
    AMOUNT VARCHAR(30),
    PROFIT INT,
    QUANTITY INT,
    CATEGORY VARCHAR(30),
    SUBCATEGORY VARCHAR(30));

--Prepare stage object
CREATE OR REPLACE STAGE COPY_DB.PUBLIC.aws_stage_copy
    url='s3:--snowflakebucket-copyoption/returnfailed/';
  
LIST @COPY_DB.PUBLIC.aws_stage_copy
```

</details>
<br>    

```sql 
 --Load data using copy command
COPY INTO COPY_DB.PUBLIC.ORDERS
    FROM @aws_stage_copy
    file_format= (type = csv field_delimiter=',' skip_header=1)
    pattern='.*Order.*'
    RETURN_FAILED_ONLY = TRUE
    
COPY INTO COPY_DB.PUBLIC.ORDERS
    FROM @aws_stage_copy
    file_format= (type = csv field_delimiter=',' skip_header=1)
    pattern='.*Order.*'
    ON_ERROR =CONTINUE
    RETURN_FAILED_ONLY = TRUE

--Default = FALSE
CREATE OR REPLACE TABLE  COPY_DB.PUBLIC.ORDERS (
    ORDER_ID VARCHAR(30),
    AMOUNT VARCHAR(30),
    PROFIT INT,
    QUANTITY INT,
    CATEGORY VARCHAR(30),
    SUBCATEGORY VARCHAR(30));

COPY INTO COPY_DB.PUBLIC.ORDERS
    FROM @aws_stage_copy
    file_format= (type = csv field_delimiter=',' skip_header=1)
    pattern='.*Order.*'
    ON_ERROR =CONTINUE
```
---
<br>

### `TRUCATECOLUMNS`

<details>
    <summary>Set Up</summary>

```sql
CREATE OR REPLACE TABLE  COPY_DB.PUBLIC.ORDERS (
    ORDER_ID VARCHAR(30),
    AMOUNT VARCHAR(30),
    PROFIT INT,
    QUANTITY INT,
    CATEGORY VARCHAR(10),
    SUBCATEGORY VARCHAR(30));

--Prepare stage object
CREATE OR REPLACE STAGE COPY_DB.PUBLIC.aws_stage_copy
    url='s3:--snowflakebucket-copyoption/size/';
  
LIST @COPY_DB.PUBLIC.aws_stage_copy
```
</details>
<br>

```sql
    
 --Load data using copy command
COPY INTO COPY_DB.PUBLIC.ORDERS
    FROM @aws_stage_copy
    file_format= (type = csv field_delimiter=',' skip_header=1)
    pattern='.*Order.*'

COPY INTO COPY_DB.PUBLIC.ORDERS
    FROM @aws_stage_copy
    file_format= (type = csv field_delimiter=',' skip_header=1)
    pattern='.*Order.*'
    TRUNCATECOLUMNS = true; 
    
SELECT * FROM ORDERS;    
```
---
<br>

### `FORCE`
<details>
    <summary>Set Up</summary>

```sql
CREATE OR REPLACE TABLE  COPY_DB.PUBLIC.ORDERS (
    ORDER_ID VARCHAR(30),
    AMOUNT VARCHAR(30),
    PROFIT INT,
    QUANTITY INT,
    CATEGORY VARCHAR(30),
    SUBCATEGORY VARCHAR(30));

--Prepare stage object
CREATE OR REPLACE STAGE COPY_DB.PUBLIC.aws_stage_copy
    url='s3:--snowflakebucket-copyoption/size/';
  
LIST @COPY_DB.PUBLIC.aws_stage_copy
```

</details>
<br>

```sql
    
 --Load data using copy command
COPY INTO COPY_DB.PUBLIC.ORDERS
    FROM @aws_stage_copy
    file_format= (type = csv field_delimiter=',' skip_header=1)
    pattern='.*Order.*'

--Not possible to load file that have been loaded and data has not been modified
COPY INTO COPY_DB.PUBLIC.ORDERS
    FROM @aws_stage_copy
    file_format= (type = csv field_delimiter=',' skip_header=1)
    pattern='.*Order.*'
   

SELECT * FROM ORDERS;    


--Using the FORCE option

COPY INTO COPY_DB.PUBLIC.ORDERS
    FROM @aws_stage_copy
    file_format= (type = csv field_delimiter=',' skip_header=1)
    pattern='.*Order.*'
    FORCE = TRUE;
    

```
---
<br>


---
id: k2cg7dgb3jamytfwlv6trum
title: File_formats
desc: ''
updated: 1670664709812
created: 1666455929137
---


## File Format Object

#### Documentation
Link: https://docs.snowflake.com/en/sql-reference/sql/create-file-format.html

<br>

- Previously, we specified the file format within the `COPY INTO ` command
- However, a better way is to define file format as object which are reusable

```sql
--Specifying file_format in Copy command
COPY INTO OUR_FIRST_DB.PUBLIC.ORDERS_EX
    FROM @MANAGE_DB.external_stages.aws_stage_errorex
    file_format = (type = csv field_delimiter=',' skip_header=1)
    files = ('OrderDetails_error.csv')
    ON_ERROR = 'SKIP_FILE_3'; 
```
<details>
    <summary>Set Up</summary>

```sql
--Creating table
CREATE OR REPLACE TABLE OUR_FIRST_DB.PUBLIC.ORDERS_EX (
    ORDER_ID VARCHAR(30),
    AMOUNT INT,
    PROFIT INT,
    QUANTITY INT,
    CATEGORY VARCHAR(30),
    SUBCATEGORY VARCHAR(30));    
    
--Creating schema to keep things organized
CREATE OR REPLACE SCHEMA MANAGE_DB.file_formats;
```
</details>
<br>

### Working with File Format Objects 
<br>

**`CREATE`** file format

```sql
--Creating file format object (default = CSV)
CREATE OR REPLACE file format MANAGE_DB.file_formats.my_file_format;

--Defining properties on creation of file format object   
CREATE OR REPLACE file format MANAGE_DB.file_formats.my_file_format
    TYPE=JSON,
    TIME_FORMAT=AUTO;    
```
<br>

**`DESCRIBE`** file format
```sql
--See properties of file format object
DESC file format MANAGE_DB.file_formats.my_file_format;
```

<br>

Using File Format Objects
```sql
--Using file format object in Copy command       
COPY INTO OUR_FIRST_DB.PUBLIC.ORDERS_EX
    FROM @MANAGE_DB.external_stages.aws_stage_errorex
    file_format= (FORMAT_NAME=MANAGE_DB.file_formats.my_file_format)
    files = ('OrderDetails_error.csv')
    ON_ERROR = 'SKIP_FILE_3'; 
```
<br>

**`ALTER`** file format
```sql
--Altering file format object
ALTER file format MANAGE_DB.file_formats.my_file_format
    SET SKIP_HEADER = 1;

--Altering the type of a file format is not possible
ALTER file format MANAGE_DB.file_formats.my_file_format
    SET TYPE = CSV;
```

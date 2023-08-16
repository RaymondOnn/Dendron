---
id: p1odxjt63jkx9byiqkcskrv
title: Stages
desc: ''
updated: 1666455591029
created: 1666455555020
---

### Stages
- Stages are locations of data files where data can be loaded from
- External Stage
  - Provider by external cloud provider
  - Exist as database object created in schema
  - `CREATE STAGE(URL, access settings)`
- Internal Stage
  - Local storage maintained by Snowflake

<br>

### Creating Stage
```sql

--Database to manage stage objects, fileformats etc.
CREATE OR REPLACE DATABASE MANAGE_DB;
CREATE OR REPLACE SCHEMA external_stages;

--Creating external stage
CREATE OR REPLACE STAGE MANAGE_DB.external_stages.aws_stage
    url='s3:--bucketsnowflakes3'
    credentials=(aws_key_id='ABCD_DUMMY_ID' aws_secret_key='1234abcd_key');

--Description of external stage
DESC STAGE MANAGE_DB.external_stages.aws_stage; 
    
--Alter external stage   
ALTER STAGE aws_stage
    SET credentials=(aws_key_id='XYZ_DUMMY_ID' aws_secret_key='987xyz');
    
--Publicly accessible staging area    
CREATE OR REPLACE STAGE MANAGE_DB.external_stages.aws_stage
    url='s3:--bucketsnowflakes3';

--List files in stage
LIST @aws_stage;

--Load data using copy command
COPY INTO OUR_FIRST_DB.PUBLIC.ORDERS
    FROM @aws_stage
    file_format= (type = csv field_delimiter=',' skip_header=1)
    pattern='.*Order.*';

```

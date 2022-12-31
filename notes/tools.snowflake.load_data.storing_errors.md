---
id: zhtekuxy18gh4hlhwtwxkai
title: Storing_errors
desc: ''
updated: 1666458507944
created: 1666458362866
---

### Saving rejected files during loading of data
```sql
    COPY INTO COPY_DB.PUBLIC.ORDERS
        FROM @aws_stage_copy
        file_format= (type = csv field_delimiter=',' skip_header=1)
        pattern='.*Order.*'
        ON_ERROR=CONTINUE
    
    -- '_last' refers to the last query
    select * from table(validate(orders, job_id => '_last'));
```

### Working with rejected records

```sql
    ---- 3) Working with rejected records ---- 
    SELECT REJECTED_RECORD FROM rejected;

    CREATE OR REPLACE TABLE rejected_values as
    SELECT 
    SPLIT_PART(rejected_record,',',1) as ORDER_ID, 
    SPLIT_PART(rejected_record,',',2) as AMOUNT, 
    SPLIT_PART(rejected_record,',',3) as PROFIT, 
    SPLIT_PART(rejected_record,',',4) as QUATNTITY, 
    SPLIT_PART(rejected_record,',',5) as CATEGORY, 
    SPLIT_PART(rejected_record,',',6) as SUBCATEGORY
    FROM rejected; 

    SELECT * FROM rejected_values;
```
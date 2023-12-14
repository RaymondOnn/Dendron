---
id: dm0n0db35tj0aiyluia2tus
title: stored_procedures
desc: ''
updated: 1670681344077
created: 1665820960974
---

Back to Main Topic: [[tools.storage.db.snowflake]]


# Stored Procedures
- Documentation: https://docs.snowflake.com/en/sql-reference/stored-procedures-javascript.html#writing-stored-procedures-in-javascript
- Udemy: https://nlbsg.udemy.com/course/snowflake-cloud-data-warehouse/learn/lecture/28718696#overview

Link: https://www.youtube.com/watch?v=7zc0UqaRWQ0

```sql
DROP DATABASE IF EXISTS RAMU;
CREATE DATABASE RAMU;
USE RAMU;

CREATE or replace TABLE log_storer(Execution_time String,Message Text);
```

- Store Procedure to 
```sql
CREATE OR REPLACE PROCEDURE captureLog(MSG STRING)
RETURNS STRING
LANGUAGE JAVASCRIPT
AS
$$

  var my_sql_command_date = "select to_timestamp(current_timestamp()) as curr_time";
  var statement1 = snowflake.createStatement( {sqlText: my_sql_command_date} );
  var result_set1 = statement1.execute();
  result_set1.next();
  var time_info= result_set1.getColumnValue(1);
  
  var my_sql_command = "INSERT INTO log_storer values ('"+time_info+"','"+MSG+"')";
  var statement1 = snowflake.createStatement( {sqlText: my_sql_command} );
  var result_set1 = statement1.execute();

$$;
```

```sql
CREATE OR REPLACE PROCEDURE my_test()
RETURNS STRING
LANGUAGE JAVASCRIPT
AS
$$
function log(msg)
{
  var my_sql_command = "call captureLog('"+msg+"')";
  var statement1 = snowflake.createStatement( {sqlText: my_sql_command} );
  var result_set1 = statement1.execute();
}

try
  {
    var x=10/10;
    log('log this message');
    log('x = '+x.toString())
    log('this is another log message')
    var m=sum(2,3);
    return 'All log captured!!!'
  }
catch(ERROR)
  {
    log(ERROR);
    return ERROR
  }
$$;


call my_test();


select * from log_storer;

```
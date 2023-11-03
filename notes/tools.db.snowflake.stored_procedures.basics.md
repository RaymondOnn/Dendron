---
id: tnzxhweocckt2c0aafi0v7e
title: Basics
desc: ''
updated: 1670680336860
created: 1670680257950
---

## Basics ##
### Stored Procedure Syntax ###
<br/>

Here we have a stored Procedure to return the row count of table

```SQL
create or replace procedure column_fill_rate_1(TABLE_NAME varchar)
    returns varchar
    language javascript
    as
    $$ --> indicate start of procedure
        
        var my_sql_command =   "select count(*) from " + TABLE_NAME + ";"
        -- execute sql statement
        var statement1 = snowflake.createStatement({sqlText:my_sql_command});
        -- storing results into result_set1
        var result_set1 = statement1.execute();
        result_set1.next()

        row_count = result_Set1.getColumnValue(1);

    return row_count;
    $$
    ;

call column_fill_Rate_1('CUSTOMER') 
```

<br/>
This stored procedure works fine. However, it's returning the wrong data type for output.<br/>
Let's change that.<br/>
<br/>

### Data Types ###  
SQL and JavaScript Data Type Mapping: https://docs.snowflake.com/en/sql-reference/stored-procedures-javascript.html#sql-and-javascript-data-type-mapping

<br/>

Note: 
* for numbers, use the **FLOAT** data type. 
* for binary & object data type, use the **Uint8Array** data type. 

<br/>

```SQL
create or replace procedure column_fill_rate_1(TABLE_NAME varchar)
    returns FLOAT
    language javascript
    as
    $$ --> indicate start of procedure
        
        var my_sql_command =   "select count(*) from " + TABLE_NAME + ";"
        -- execute sql statement
        var statement1 = snowflake.createStatement({sqlText:my_sql_command});
        -- storing results into result_set1
        var result_set1 = statement1.execute();
        result_set1.next()

        row_count = result_Set1.getColumnValue(1);

    return row_count;
    $$
    ;

call column_fill_Rate_1('CUSTOMER') 
```
### Stored Procedure Methods ###

<br/>
Let's try to return the statement object. It will return a JSON object <br/>
<strong>NOTE: Can be used to see the available javascript methods</strong><br/>


<br/>


```SQL
create or replace procedure column_fill_rate_1(TABLE_NAME varchar)
    returns VARIANT NOT NULL
    language javascript
    as
    $$ --> indicate start of procedure
        
        var my_sql_command =   "select count(*) from " + TABLE_NAME + ";"
        var statement1 = snowflake.createStatement({sqlText:my_sql_command});
        var result_set1 = statement1.execute();
        result_set1.next()

        row_count = result_Set1.getColumnValue(1);

    return statement1;
    $$
    ;

call column_fill_Rate_1('CUSTOMER') 
```

### Argument Constraints ###
<br/>

NOTE: Argument names are **CASE-INSENSITIVE** in the SQL portion of the stored procedure code BUT are **CASE-SENSITIVE** in the javascript portion of the stored procedure code 

```SQL
create or replace procedure f(argument1 VARCHAR)
    returns VARCHAR
    language javascript
    as
    $$ 
        var local_variable_1 = ARGUMENT1; // CORRECT
        var local_variable_2 = argument1; // INCORRECT

    return local_variable2;
    $$;

call f('prod')
```
BEST PRACTICE: When passing arguments to stored procedure, use **UPPER CASE**!!

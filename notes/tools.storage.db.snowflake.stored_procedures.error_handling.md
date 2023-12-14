---
id: r6k1hy1g8bwb5m0qppmdptp
title: Error_handling
desc: ''
updated: 1670680689634
created: 1670680689634
---


## Error Handling ##
### Error Scenarios ###

<br/>

Examples
- What if table is empty?
- What if column value of table is NULL?
- What if you pass table which do not exist?
- What if column data type is varient or json?
- What if procedure fails halfway during execution due to external reasons?


### What if table is empty and junk values is passed into argument? ###
- Known Errors: Use IF-ELSE conditions to filter out errors
- UnKnown Errors: TRY & CATCH block to contain errors
<br/>
<br/>

```SQL
// What if table is empty and junk values is passed into argument?

CREATE OR REPLACE PROCEDURE column_fill_rate(TABLE_NAME varchar)
    returns VARIANT NOT NULL
    language javascript
    as 
    $$
        // To ensure table name is valid, we can check using regex
        var input_pattern = "SELECT RLIKE('" + TABLE_NAME + "', '[a-zA-Z0-9_]+')"
        var statement0 = snowflake.createStatement({sqlText: input_pattern});

        // Return input_pattern
        var result_set0 = statement0.execute();
        result_set0.next();
        reg_status = result_set0.getColumnValue(1)

        if (reg_status == false) {
            return TABLE_NAME + "is not a table"
        }

        // To tackle unexpected scenarios, we can use the TRY and CATCH block
        try{
            var my_sql_command = "select COUNT(*) CNT FROM " + TABLE_NAME + ":"
            var statement1 = snowflake.createStatement({sqlText:my_sql_command});
            var result_set1 = statement1.execute();
            result_set1.next();
        } catch (err) {
            return "Failed: " + err
        }
        var cnt = result_set1.getColumnValue(1);

        if (cnt == 0) {
            return TABLE_NAME + " is empty"
        }

        try{
            var my_sql_command2 = "select * FROM " + TABLE_NAME + " LIMIT 10:"
            var statement2 = snowflake.createStatement({sqlText:my_sql_command2});
            var result_set2 = statement2.execute();
        } catch (err) {
            return "Failed: " + err
        }
        
        var array_of_rows = [];

        var row_num = 0;
        row_as_json = {};

        var column_name;

        while (result_set2.next()) {
            
            // Loops across columns
            for (var col_num = 0; col_num < result_set2.getColumnCount()>; col_num = col_num + 1) {
                var col_name = result_set2.getColumnName(col_num+1);
                var my_sql_command3 = "select round(count(*)/"+ cnt +",2) RW_CNT FROM " + table_name + " where " + col_name + " IS NOT NULL;"

                var statement3 = snowflake.createStatement({sqlText: my_sql_command3})

                result_set3 = statement3.execute();
                result_set3.next();
                
                var col_value = result_set3.getColumnValue(1);
                
                row_as_json = {ColumnName:col_num, ColumnValue: col_value}
                array_of_rows.push(row_as_json)
            }
        }
        table_as_json = {"key1": array_of_rows };

        try{
            for (var col_num = 0, col_num < result_set2.getColumnCOunt(); col_num = col_num + 1) {
                var my_sql_command4 = "insert into TABLE_FILL_RATE VALUES(:1, :2)"
                var statement4 = snowflake.createStatement({
                    sqlText: my_sql_command4,
                    Binds: [table_as_json.key1[col_num].ColumnName, table_as_json.key1[col_num].ColumnValue]
                });
                statement4.execute();
            }
        } catch (err) {
            return "Failed: " + err
        }

    $$;
```
<br/>

Until now, the stored procedures was able to fail gracefully.
This is fine if this stored procedure is called from an application.
However, if this stored procedure is called by an ETL job, we **expect the error to bubble and the procedure to fail**.
Otherwise, the procedure might show that it has succeeded when it did not and negatively affect downstream jobs.

<br/>

```javascript
CREATE OR REPLACE TABLE error_log (error_code string, error_state string, error_message string, stack_trace string);

CREATE OR REPLACE PROCEDURE column_fill_Rate(TABLE_NAME varchar)
    returns VARIANT
    language Javascript
    AS
    $$
        // To ensure table name is valid, we can check using regex
        var input_pattern = "SELECT RLIKE('" + TABLE_NAME + "', '[a-zA-Z0-9_]+')"
        var statement0 = snowflake.createStatement({sqlText: input_pattern});

        var result_set0 = statement0.execute();
        result_set0.next();
        reg_status = result_set0.getColumnValue(1)

        // throw will fail the stored procedure, while giving specified error message
        if (reg_status == false) {
            throw TABLE_NAME + "is not a table"
        }

        // To tackle unexpected scenarios, we can use the TRY and CATCH block
        try{
            var my_sql_command = "select COUNT(*) CNT FROM " + TABLE_NAME + ":"
            var statement1 = snowflake.createStatement({sqlText:my_sql_command});
            var result_set1 = statement1.execute();
            result_set1.next();
        } catch (err) {
            // Record error details in error_log table
            snowflake.execure(
                {
                    sqlText: 'INSERT INTO error_log VALUES(?, ?, ?, ?)',
                    binds: [err.code, err.state, err.message, err.stackTraceTxt]
                };
                throw err.message
            )
        }
        var cnt = result_set1.getColumnValue(1);

        if (cnt == 0) {
            return TABLE_NAME + " is empty"
        }

        try{
            var my_sql_command2 = "select * FROM " + TABLE_NAME + " LIMIT 10:"
            var statement2 = snowflake.createStatement({sqlText:my_sql_command2});
            var result_set2 = statement2.execute();
        } catch (err) {
            // Record error details in error_log table
            snowflake.execure(
                {
                    sqlText: 'INSERT INTO error_log VALUES(?, ?, ?, ?)',
                    binds: [err.code, err.state, err.message, err.stackTraceTxt]
                };
                throw err.message
            )
        }
        
        var array_of_rows = [];

        var row_num = 0;
        row_as_json = {};

        var column_name;

        while (result_set2.next()) {
            
            // Loops across columns
            for (var col_num = 0; col_num < result_set2.getColumnCount()>; col_num = col_num + 1) {
                var col_name = result_set2.getColumnName(col_num+1);
                var my_sql_command3 = "select round(count(*)/"+ cnt +",2) RW_CNT FROM " + table_name + " where " + col_name + " IS NOT NULL;"

                var statement3 = snowflake.createStatement({sqlText: my_sql_command3})

                result_set3 = statement3.execute();
                result_set3.next();
                
                var col_value = result_set3.getColumnValue(1);
                
                row_as_json = {ColumnName:col_num, ColumnValue: col_value}
                array_of_rows.push(row_as_json)
            }
        }
        table_as_json = {"key1": array_of_rows };

        try{
            for (var col_num = 0, col_num < result_set2.getColumnCOunt(); col_num = col_num + 1) {
                var my_sql_command4 = "insert into TABLE_FILL_RATE VALUES(:1, :2)"
                var statement4 = snowflake.createStatement({
                    sqlText: my_sql_command4,
                    Binds: [table_as_json.key1[col_num].ColumnName, table_as_json.key1[col_num].ColumnValue]
                });
                statement4.execute();
            }
        } catch (err) {
            return "Failed: " + err
        }

    $$;
```
 ---
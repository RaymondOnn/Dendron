---
id: v263fvyfu4wi3hc36a0uga6
title: Logging_errors
desc: ''
updated: 1670680761777
created: 1670680761777
---


## Logging Error Messages ## 


```Javascript
// Logging procedure

set do_log = true // true to enable logging, false (or undefined) to disable
set log_table = 'my_log_table'; // the name of the temp table where log messages go.

CREATE OR REPLACE PROCEDURE do_log (MSG string)
    returns STRING
    language JAVASCRIPT
    execute AS CALLER
    AS
    $$
        // checks for session variable do_log = true
        try {
            var stmt = snowflake.createStatement({sqlText: `select $do_log`}).execute();
        } catch(ERROR) {
            return ''; //Do not log
        }
        stmt.next(); // initialize cursor to obtain value
        if(stmt.getColumnValue(1)==true) {
            try {
                snowflake.createStatement({sqlText: `CREATE TEMP TABLE IDENTIFIER ($log_table) if not exists(ts number, msg string)`}).execute();
                snowflake.createStatement({sqlText: `INSERT INTO IDENTIFIER($log_table) VALUES (:1, :2)`, binds:[Date.now(), MSG]}).execute()
            
            } catch(ERROR) {
                throw ERROR;
            }
        }
    $$;
```
<br/>

#### How to implement? ####
- Initialize variable to store logs
- Accumulate log messages throughout the stored procedures
- Insert into log table at the end of the procedure

<br/>

```javascript
CREATE OR REPLACE PROCEDURE column_fill_Rate(TABLE_NAME varchar)
    returns VARIANT
    language Javascript
    execute AS CALLER
    AS
    $$
        var accumulated_log_messages = ''

        function log(msg) {
            snowflake.createStatement({
                sqlTextL `CALL do_log(:1)`,
                binds:[msg]
            }).execute();
        }

        // To ensure table name is valid, we can check using regex
        var input_pattern = "SELECT RLIKE('" + TABLE_NAME + "', '[a-zA-Z0-9_]+')"
        var statement0 = snowflake.createStatement({sqlText: input_pattern});

        var result_set0 = statement0.execute();
        result_set0.next();
        reg_status = result_set0.getColumnValue(1)

        // Add to log messages
        accumulated_log_messages += 'regular expression result: ' + reg_status + '/n';

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
        // Add to log messages
        accumulated_log_messages += 'count of records: ' + cnt + '/n';

        try{
            var my_sql_command2 = "select * FROM " + TABLE_NAME + " LIMIT 10:"
            var statement2 = snowflake.createStatement({sqlText:my_sql_command2});
            var result_set2 = statement2.execute();
        } catch (err) {
            // Record error details in error_log table
            snowflake.execute(
                {
                    sqlText: 'INSERT INTO error_log VALUES(?, ?, ?, ?)',
                    binds: [err.code, err.state, err.message, err.stackTraceTxt]
                };
                throw "Failed: when trying to get schema of the table";
            )
        }
        // Add to log messages
        accumulated_log_messages += 'column type pf result set 2: ' + result_set2.getColumnType(1) + '/n';

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
        // Even if procedure failed, finally block will run accordingly and ensure the messages are logged   
        } finally {
            if(accumulated_log_messages != '') {
                log(accumulated_log_messages)
            }
        }

    $$;

```
 ---
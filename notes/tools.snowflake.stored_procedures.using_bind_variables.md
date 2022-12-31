---
id: hosns41wppvwct3otehuukr
title: Using_bind_variables
desc: ''
updated: 1670680634086
created: 1670680634086
---


## Using Bind Variables ##
https://docs.snowflake.com/en/sql-reference/stored-procedures-javascript.html#label-stored-procedures-binding-variables

```SQL
CREATE OR REPLACE PROCEDURE column_fill_Rate_bind_var(TABLE_NAME varchar)
    returns VARIANT NOT NULL
    language JAVASCRIPT
    AS 
    $$
        var array_of_rows = [];
        row_as_json = {};

        // Get result_set 
        var my_sql_command = "select * from " + TABLE_NAME + " LIMIT 10;"
        var statement1 = snowflake.createStatement({sqlText:my_sql_command});
        var result_set1 = statement1.execute();
        
        // CUSTOMER_TRANSPOSED TABLE has two columns, ColumnName & ColumnValue
        var insert_cmd = "INSERT INTO CUSTOMER_TRANSPOSED VALUES(:1, :2)"

        // Proceeds row by row basis
        while (result_set1.next()) {
            
            // Loops across columns
            for (var col_num = 0; col_num < result_set1.getColumnCount()>; col_num = col_num + 1) {
                var col_name = result_set1.getColumnName(col_num+1);
                var col_value = result_set1.getColumnValue(col_num+1);

                if (col_name=='C_NAME') {
                    col_value='JOHN'
                } else {
                    col_value
                }

                row_as_json = {ColumnName:col_num, ColumnValue: col_value}
                array_of_rows.push(row_as_json)


                // ":1, :2" acts as placeholder for the variables binded here
                snowflake.execute(
                    {
                        sqlText: insert_cmd,
                        binds: [col_name, col_value]
                    }
                ); 

                /*
                Another possible way:
                snowflake.execute(
                    {
                        sqlText: "INSERT INTO CUSTOMER_TRANSPOSED VALUES(?,?)",
                        binds: [col_name, col_value]
                    }
                );
                */ 
            }
        }
        
        table_as_json = {"key1": array_of_rows };
    
    // output as json
    return table_as_json;
    $$

call column_fill_rate_loops('CUSTOMER') 
```

---

## Table Fill Rate Procedure ## 

<br/>
Combining All Concepts into One

```SQL
CREATE OR REPLACE PROCEDURE column_fill_rate(TABLE_NAME varchar )
    returns VARIANT NOT NULL
    language javascript
    AS
    $$
        // Get number of records
        var my_sql_command = "select COUNT(*) CNT FROM " + TABLE_NAME + ":"
        var statement1 = snowflake.createStatement({sqlText:my_sql_command});
        var result_set1 = statement1.execute();
        result_set1.next()

        var my_sql_command2 = "select * FROM " + TABLE_NAME + " LIMIT 10:"
        var statement2 = snowflake.createStatement({sqlText:my_sql_command2});
        var result_set2 = statement2.execute();
        result_set2.next()

        var cnt = result_set1.getColumnValue(1)
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

        for (var col_num = 0, col_num < result_set2.getColumnCOunt(); col_num = col_num + 1) {
            var my_sql_command4 = "insert into TABLE_FILL_RATE VALUES(:1, :2)"
            var statement4 = snowflake.createStatement({
                sqlText: my_sql_command4,
                Binds: [table_as_json.key1[col_num].ColumnName, table_as_json.key1[col_num].ColumnValue]
            });
            statement4.execute();
        }
    return table_as_json    
    $$;
```
---
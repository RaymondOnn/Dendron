---
id: vy85jmcgatxvhwvrys4hv1r
title: Using_control_statements
desc: ''
updated: 1670680555000
created: 1670680555000
---


## Using Control Statements ##
### WHILE Loops and FOR Loops ###
<br/>

NOTE: 
- while loops: Proceeds row by row
- for loops: Proceeds column by column
<br/>

```SQL
CREATE OR REPLACE PROCEDURE column_fill_rate_loops(TABLE_NAME VARCHAR)
    returns VARIANT NOT NULL
    language JAVASCRIPT
    AS
    $$
        var array_of_rows = [];
        row_as_json = {};

        var my_sql_command = "select count(*) ABC, count(*) DEF from " + TABLE_NAME + ";"
        var statement1 = snowflake.createStatement({sqlText:my_sql_command});
        var result_set1 = statement1.execute();
        
        // Proceeds row by row basis
        // result_set1.next() -> Next value in result_set
        while (result_set1.next()) {
            // Loops across columns
            // col_num start at 0, increment by 1 as long as it is smaller than column count
            for (var col_num = 0; col_num < result_set1.getColumnCount()>; col_num = col_num + 1) {
                var col_name = result_set1.getColumnName(col_num+1);
                var col_value = result_set1.getColumnValue(col_num+1);

                row_as_json = {ColumnName:col_num, ColumnValue: col_value}
                array_of_rows.push(row_as_json)
            }
        }
        
        table_as_json = {"key1": array_of_rows };
    
    return result_set1.next();
    $$;    
```
<br/>

### Adding If Else Clauses ###
<br/>

```SQL
CREATE OR REPLACE PROCEDURE column_fill_rate_loops_if_else(TABLE_NAME VARCHAR)
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
        
        // Proceeds row by row basis
        // result_set1.next() -> Next value in result_set, will return true if value exists, else false
        while (result_set1.next()) {
            // Loops across columns
            // col_num start at 0, increment by 1 as long as it is smaller than column count
            for (var col_num = 0; col_num < result_set1.getColumnCount()>; col_num = col_num + 1) {
                var col_name = result_set1.getColumnName(col_num+1);
                var col_value = result_set1.getColumnValue(col_num+1);

                if (col_name) {
                    col_value='JOHN'
                } else {
                    col_value
                }

                row_as_json = {ColumnName:col_num, ColumnValue: col_value}
                array_of_rows.push(row_as_json)
            }
        }
        
        table_as_json = {"key1": array_of_rows };
    
    // output as json
    return table_as_json;
    $$;    

call column_fill_rate_loops('CUSTOMER')    
```
---
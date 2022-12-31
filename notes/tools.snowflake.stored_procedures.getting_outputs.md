---
id: cby5kzs5k633bttmdk865gt
title: Getting_outputs
desc: ''
updated: 1670680432291
created: 1670680432291
---


## Returning Results from Stored Procedures ##
### Stored Procedures will only return only one result ###

<br/>

- Stored Procedure can only return **SINGLE** output, not multiple outputs.
Hence, it's important to think about the structure of the output
- For multiple outputs, we can use the JSON structure. The idea is to return the outputs in a JSON object and then parse the JSON object into a table

```JSON
{
    "key1": [
        {
            "ColumnName": "ABC", 
            "ColumnValue": 0.98
        },
        {
            "ColumnName": "DEF", 
            "ColumnValue": 0.81
        }
    ]
}
```
<BR/>

### Working with JSON object using SQL ###
<br/>

```SQL 
CREATE OR REPLACE TRANSIENT TABLE JSON_TBL(FILL_RATE VARIANT)

INSERT INTO JSON_TBL
SELECT 
    PARSE_JSON(
        '{
            "key1": [
                {
                    "ColumnName": "ABC", 
                    "ColumnValue": 0.98
                },
                {
                    "ColumnName": "DEF", 
                    "ColumnValue": 0.81
                }
            ]
        }'
    )

SELECT
    f.value:ColumnName,
    f.valueLColumnValue
FROM JSON_TBL, table(FLATTEN(fill_rate:key1)) f
```

### Generating the JSON object using Javascript ###
<br/>

```SQL
CREATE OR REPLACE PROCEDURE column_fill_rate_output_structure(TABLE_NAME VARCHAR)
    RETURNS VARIANT NOT NULL
    LANGUAGE JAVASCRIPT
    AS
    $$
        // initialize variables
        var array_of_rows = [];
        rows_as_json = {};

        // get result_set
        var my_sql_command = "select count(*) ABC, count(*) DEF from " + TABLE_NAME + ";"
        var statement1 = snowflake.createStatement({sqlText:my_sql_command});
        var result_set1 = statement1.execute();
        result_set1.next() // initialize cursor, required to navigate result_set

        // Generate the JSON object one key-value pair at a time
        ColumnName = result_set1.getColumnName(1);
        ColumnValue = result_set1.getColumnValue(1);
        row_as_json = {ColumnName:ColumnName, ColumnValue: 9/10}
        array_of_rows.push(row_as_json) //Load row_as_json into array

        ColumnName = result_set1.getColumnName(2);
        ColumnValue = result_set1.getColumnValue(2);
        row_as_json = {ColumnName:ColumnName, ColumnValue: 8/10}
        array_of_rows.push(row_as_json)

        table_as_json = {"key1": array_of_rows };

    RETURN table_as_json;
    $$;

-- NOTE: Only a limited list of Javascripts methods are allowed. 
-- Code is only compiled upon execution. Hence, the error will only be thrown will the stored procedure is executed
-- The other javascript methods will throw error when running the stored procedure e.g. floor


// Hence, to fix the output issue    

    var my_sql_command = "select floor(9/10) ABC, floor(8/10) DEF from " + TABLE_NAME + ";"
```
<br/>

### Parse JSON Output Return from Stored Procedure ###
<br/>

To get the return from the stored procedure, we can do this.  
However, it only works once since you are referencing the LAST_QUERY_ID

```SQL
SELECT
    f.value:ColumnName,
    f.valueLColumnValue
FROM TABLE(RESULT_SCAN(LAST_QUERY_ID())) as result, table(FLATTEN(COLUMN_FILL_RATE_OUTPUT_STRUCTURE:key1)) f
```
---
---
id: z73hh51pjbyyn88b8j88q6a
title: scd
desc: ''
updated: 1710022071378
created: 1710022060977
---

### Slowly Changing Dimensions
- The general process is explained as follows:
  1. Load the common model table from staging.
  2. Merge existing data with incoming data.
     1. Insert new records.
     2. Update existing records.
  3. Truncate the common model table.
  4. Load the common model table with key fields necessary to populate fact tables.

#### The Empty Record
- Every single dimension table should have an empty record. 
- Empty records should arise naturally as a result of processing the dimension. 
- Some fact records will not have a dimension attached to them and the empty record takes care of that scenario.
- In some cases it is acceptable to preload an empty record. Sometimes, you can have an empty record as part of the dimension processing just to make sure it's always there like the example below.
    ```sql
    INSERT INTO cm.DimCustomer(
        CustomerID,
        FirstName,
        LastName,
    )
    SELECT
        CustomerID,
        FirstName,
        LastName,
    FROM sf.CustomerData
    
    UNION
    
    SELECT
        '' AS CustomerID,
        '' AS FirstName,
        '' AS LastName,
    ```
- The empty record should be identified by an empty record 
    - i.e. an empty string value for SourceSystemKey. 
    - Do not use the contrived key to identify empty records. 
    - the contrived key is only meant for joining to fact records and necessary maintenance.
> Pro Tip: Do not use the contrive key to identify empty records.

#### SCD Processing Types

Below is a table of SCD processing types and descriptions of how they work.

 | Type | Short Description   | Description                                                                                    | Real World Use Case                                |
 | ---- | ------------------- | ---------------------------------------------------------------------------------------------- | -------------------------------------------------- |
 | 0    | Do nothing          | The data is functionally immutable.                                                            | DimDate                                            |
 | 1    | Overwrite           | Do not store history. New values simply overwrite the old.                                  | Only care about the latest values.                |
 | 2    | Add a row           | Add a new row that represents the current state of the data and deprecate the old row.         | Most common method for SCD.                |
 | 3    | Add a column        | Add a column to the table. Good only to store 1 step of history. Poor way to manage SCDs.      | Keeping track of maiden names                      |
 | 4    | Add a history table | Store history in a separate table and the main table represents the current state of the data. | This is how SQL Server implements Temporal Tables. |
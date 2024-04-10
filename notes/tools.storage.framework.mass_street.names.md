---
id: wt208junqbi5pyd5sdg50xn
title: names
desc: ''
updated: 1710022134761
created: 1710022127519
---


#### Naming Conventions

-   When giving names to database objects, use Pascal case.

##### Schemas

-   Most schemas are predefined by this framework.
-   However, in ODS, staging tables should have schema names that are some sort of recognizable acronym for where the data came from for e.g. `boa`=Bank of America, `ss`= Smart Soft, `sf`=Sales Force.

##### Stored Procedures

-   Stored procedures are named according to their function in the pipeline.

| Process Step              | Naming Convention                           | Naming Model                    | Example                       |
| ------------------------- | ------------------------------------------- | ------------------------------- | ----------------------------- |
| Pull Data                 | `pull` + staging table the proc dumps to.   | `usp_Pull[DescriptionOfData]`   | `usp_PullCustomerData`        |
| Clean Data                | `clean` + staging table the proc dumps to.  | `usp_Clean[DescriptionOfData]`  | `usp_CleanCustomerData`       |
| Process MDM               | `processMDM` + dimension name               | `usp_ProcessMDM[DimensionName]` | `usp_ProcessMDMDimCustomer`   |
| Process Dimensions        | `process` + dimension name                  | `usp_Process[DimensionName]`    | `usp_ProcessDimCustomer`      |
| Process Facts             | `process` + table name it works on.         | `usp_Process[FactTableName]`    | `usp_ProcessFactCustomer`     |
| Finalize And Audit        | Name should be descriptive.                 | `usp_[DescribeProcess]`         | `usp_MarkRecordsAsProcessed`  |
| Populate Reporting Tables | `load` + table name that it loads + `table` | `usp_Load[TableName]Table`      | `usp_LoadCustomerReportTable` |
| Monitoring                | Name should describe the process.           | `usp_[DescribeProcess]`         | `usp_DisplayTablesNotLoading` |

##### Tables

| Table Type                                   | Naming Convention                                                                                                                                                                                                 | Naming Model                        | Example                    |
| -------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------- | -------------------------- |
| Common Model Tables                          | Common model tables are the same as warehouse tables they mimic but stripped of all indexes, keys, and most constraints with the exception of the index on the column used to match records.                      | `Dim[BaseDimensionTableName]`       | `DimCustomer`              |
| Conventional Staging Tables                  | Table names should describe what the data is and end with `Data`. Tables should also be assigned to a schema that identifies the source system of the data.                                                       | `[DescriptiveName]Data`             | `CustomerData`             |
| Dimension Tables                             | All dimension tables start with `Dim`.                                                                                                                                                                            | `Dim[YourDimensionName]`            | `DimCustomer`              |
| Fact Tables                                  | All fact tables start with`Fact`.                                                                                                                                                                                 | `Fact[YourFactTableName]`           | `FactCustomerDetail`       |
| Junk Dimension Tables                        | All junk dimension names end in the word `Information` in addition to inheriting the convention for naming standard dimensions.                                                                                   | `Dim[YourDimensionName]Information` | `DimCustomerInformation`   |
| MDM Tables                                   | Named after the data they are storing and end in MasterData.                                                                                                                                                      | `[Dimension]MasterData`             | `CustomerMasterData`       |
| Reporting Tables                             | Should be obviously named with spaces between words.                                                                                                                                                              | `<no model>`                        | `Customer Activity Report` |
| Staging Tables Designed For Historical Loads | Tables that are specially modified to load large amounts of historical data should share the name of the stage table that manages the normal batch process with the addition of the word "Historical" at the end. | `[DescriptiveName]DataHistorical`   | `CustomerDataHistorical`   |

##### Columns

-   All names should plainly and easily describe the information held in that column. There should be no abbreviations.
-   Columns in staging tables need to conform to the naming conventions of the source system. As a matter of fact, the column names should be identical to the source system.
-   Columns in the reporting database should be named in a manner so that they are easily readable by users which usually just means putting a space in a column name.
-   Boolean columns should be named as though a question that starts with the word 'Is' for e.g. `IsProcessed`, `IsActive`, `IsRetired`

##### Indexes

-   Indexes should be named so it is obvious what table and column the index is on and what kind of index it is.

| Index Type    | Naming Model                         | Example                             |
| ------------- | ------------------------------------ | ----------------------------------- |
| Clustered     | `CIDX*[YourTableName]*[ColumnName]`  | `CIDX_DimCustomer_CustomerCK`       |
| Non-Clustered | `NCIDX*[YourTableName]*[ColumnName]` | `NCIDX_DimCustomer_SourceSystemKey` |

##### Primary Keys

| Object Type       | Naming Convention                                                                                            | Naming Model                  | Example      |
| ----------------- | ------------------------------------------------------------------------------------------------------------ | ----------------------------- | ------------ |
| Dimension Table   | Name of the dimension w/o 'Dim' and adding a `CK` (contrived key) at the end.                                | `[DimensionNameWithOutDim]CK` | `CustomerCK` |
| Fact Table        | Fact table primary keys should be an atomic autoincremented number so individual rows are easily identified. | `Row`                         | `RowID`      |
| Master Data Table | MDM table primary keys should share the name of the table and end in EK (enterprise key).                    | `[TableName]EK`               | `CustomerEK` |
| Staging Table     | All staging tables have identically named primary keys.                                                      | `ETLKey`                      | `ETLKey`     |
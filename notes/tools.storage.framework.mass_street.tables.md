---
id: 6dkkmk4g7fz9k6c9vq7skfv
title: tables
desc: ''
updated: 1710005328488
created: 1710003034196
---


### Tables

#### Dimension Tables

-   Dimensions that are not junk dimensions and implemented as physical tables

```sql
DROP TABLE IF EXISTS [dbo].[DimYourDimensionName]
GO
​
SET ANSI_NULLS ON
GO
​
SET QUOTED_IDENTIFIER ON
GO
​
CREATE TABLE [dbo].[DimYourDimensionName](
	[YourDimensionNameCK] [bigint] IDENTITY(1,1) NOT NULL,
	[CreatedBy] [nvarchar](50) NULL,
	[CreatedOn] [datetime] NULL,
	[UpdatedBy] [nvarchar](50) NULL,
	[UpdatedOn] [datetime] NULL,
	[SourceSystem] [nvarchar](100) NULL,
	[SourceSystemKey] [nvarchar](100) NULL,
	[EffectiveFrom] [datetime] NULL,
	[EffectiveTo] [datetime] NULL,
	[IsMostRecentRecord] [bit] NULL
 CONSTRAINT [PK_YourDimensionName] PRIMARY KEY CLUSTERED
(
	[YourDimensionNameCK] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
```

#####   Standard Columns
-   `CreatedBy: nvarchar(50)`
    -   Who created the record.
    -   Any kind of record creation process should include the code: `CreatedBy = SYSTEM_USER`
-   `CreatedOn: datetime`
    - When the record was created.
    - Any kind of record creation process should include the code: `CreatedOn= CURRENT_TIMESTAMP`
- `UpdatedBy: nvarchar(50)`
    - Who updated the record.
    - If there are any update statements ran against the table, this column should be populated as part of that process. This is especially important when engineers have to make manual adjustments to data. 
    - Your SQL UPDATE statement should include: `UpdatedBy = SYSTEM_USER`
- `UpdatedOn: datetime`
    - When the record was updated.
    - If there are any update statements ran against the table, this column should be populated as part of that process. This is especially important when engineers have to make manual adjustments to data. 
    - Your SQL UPDATE statement should include: `UpdatedOn = CURRENT_TIMESTAMP`
- `SourceSystem: nvarchar(100)`
    - The source system where the data came from. This value is populated from a column of the same name in the staging table.
- `SourceSystemKey: nvarchar(100)`
    - The primary key of the record from the source system.
    - Strictly speaking, this does not have to be the primary key of a source table. It does not even have to be an atomic value. It can be concatenated value. 
    - **The important thing is that the value uniquely identify the row, and that row can be tracked back to a record in the source system.**
- `EffectiveFrom: datetime`
    - If the processing of the dimension requires that history be kept, this column indicates when the row began to represent the most recent version of the data.
    - Usually shares a value with `CreatedOn` 
- `EffectiveTo: datetime`
    - If the processing of the dimension requires that history be kept, this column indicates when the row ceased to represent the most recent version of the data.
    - `IsMostRecentRecord = 1`: the value of `EffectiveTo` is the highest date in the date dimension.
    - `IsMostRecentRecord = 0`, then the value of this column is set to the day it stopped being true. 
    - This has some challenges if there's an intraday change.
        - My solution was to begin retiring records by decrementing a minute amount of time. That is the reason the data type is datatime and not just date. I will illustrate this in the section on loading a Type II SCD.
- `IsMostRecentRecord: bit`
  - Indicates if row represents the most up to date version of that data.

#### Fact Tables
- The sample fact table file only introduces one new column to the mix: `TransactionID`.

``` sql
USE YourEDW

DROP TABLE IF EXISTS [dw].[FactYourFactTableName]
GO


SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO

SET ARITHABORT ON
GO

CREATE TABLE [dw].[FactYourFactTableName](
	[RowID] [bigint] IDENTITY(1,1) NOT NULL,
	--dimension keys
	[TransactionID] NVARCHAR(100) NULL,
	--your columns.
	[CreatedOn] [datetime] NULL,
	[CreatedBy] [nvarchar](50) NULL,
	[UpdatedOn] [datetime] NULL,
	[UpdatedBy] [nvarchar](50) NULL,
	[SourceSystem] [nvarchar](50) NULL,
	[UniqueDims]  AS (CONVERT([varbinary](35),hashbytes('SHA1',concat(
	CONVERT([nvarchar](35),[DimKey1CK],(0)),
	CONVERT([nvarchar](35),[DimKey2CK],(0)),
	CONVERT([nvarchar](35),[TransactionID],(0))
	)),(0))) PERSISTED,
	--unique rows is optional
	[UniqueRows]  AS (CONVERT([varbinary](35),hashbytes('SHA1',concat(
	CONVERT([nvarchar](35),[Column1],(0)),
	CONVERT([nvarchar](35),[Column2],(0))
	)),(0))) PERSISTED,
 CONSTRAINT [PK_prices] PRIMARY KEY CLUSTERED 
(
	[RowID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY],
 CONSTRAINT [IX_FactYourFactTableName] UNIQUE NONCLUSTERED 
(
	[UniqueDims] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

--index is optional
DROP INDEX IF EXISTS [NCIDX_FactYourFactTableName_UniqueDims] ON [dw].[FactYourFactTableName]
GO


CREATE NONCLUSTERED INDEX [NCIDX_FactYourFactTableName_UniqueDims] ON [dw].[FactYourFactTableName]
(
	[UniqueDims] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON, OPTIMIZE_FOR_SEQUENTIAL_KEY = OFF) ON [PRIMARY]
GO

--index is optional
DROP INDEX IF EXISTS [NCIDX_FactYourFactTableName_UniqueRows] ON [dw].[FactYourFactTableName]
GO


CREATE NONCLUSTERED INDEX [NCIDX_FactYourFactTableName_UniqueRows] ON [dw].[FactYourFactTableName]
(
	[UniqueRows] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON, OPTIMIZE_FOR_SEQUENTIAL_KEY = OFF) ON [PRIMARY]
GO
```

##### Standard Columns

- `TransactionID: NVARCHAR(100)`
    - This is your cheat column.
    - Theoretically, the primary key of a fact table is the combination of all the dimensions attached to that fact table plus any degenerate dimensions. This should be the case the grand majority of the time.
    - However, nature is messy and you will not always be able to pull this off. This is where you can leverage your cheat column.
    - The `TransactionID` column is part of the hash for populating `UniqueDims`. You can drop data into the column without having to significantly change the hash code which comes in super handy when things get added to the table.
    - `TransactionID` does not conform to First Normal Form. You can put more than one column of data in there, provided you separate your columns by a pipe. And this is where it comes in handy.
    - If you have some edge case where uniqueness isn't determined by the dimensions, then you can keep stuffing data into `TransactionID` until you finally get the uniqueness you are looking for.
- `CreatedBy: nvarchar(50)`
    - Who created the record.
    - Any kind of record creation process should include the code: `CreatedBy = SYSTEM_USER`
-   `CreatedOn: datetime`
    - When the record was created.
    - Any kind of record creation process should include the code: `CreatedOn= CURRENT_TIMESTAMP`
- `UpdatedBy: nvarchar(50)`
    - Who updated the record.
    - If there are any update statements ran against the table, this column should be populated as part of that process. This is especially important when engineers have to make manual adjustments to data. 
    - Your SQL UPDATE statement should include: `UpdatedBy = SYSTEM_USER`
- `UpdatedOn: datetime`
    - When the record was updated.
    - If there are any update statements ran against the table, this column should be populated as part of that process. This is especially important when engineers have to make manual adjustments to data. 
    - Your SQL UPDATE statement should include: `UpdatedOn = CURRENT_TIMESTAMP`
- `SourceSystem: nvarchar(100)`
    - The source system where the data came from. This value is populated from a column of the same name in the staging table.
- `UniqueRows: varbinary(35)`
    - A computed column containing whatever extra non dimension column you need to make a row unique. 
    - This value is passed back to the staging table for comparison later on. 
    - It is used to determine if the stage table record made it to the fact table ok.
- `UniqueDims: varbinary(35)`
    - A computed column containing a **hash of all keys** from dimensions and whatever you tossed in `TransactionID`. 
    - This value is passed back to the staging table for comparison later on. 
    - It is used to determine if the stage table record made it to the fact table ok.

#### Indexed Views
- Every table in your EDW is mirrored by a view of that table in the dbo schema. That view is a simple select statement on the source table, absent the audit columns except the `MostRecentRecord` column from dimensions.
- These views are what data analyst use to access the data warehouse. They do not need to be exposed to either the base tables, or the columns used to run and maintain the database.
- To optimize performance, create an index on it.
``` sql
USE YourEDW
​
GO
DROP VIEW IF EXISTS DimYourDimensionName 
GO
​
CREATE VIEW DimYourDimensionName
 
WITH SCHEMABINDING  
AS  
SELECT
YourDimensionNameCK,
Column1,
Column2,
ColumnN,
IsMostRecentRow
FROM [dw].[DimYourDimensionName]
GO  
--Create an index on the view.  
CREATE UNIQUE CLUSTERED INDEX CIDX_DimYourDimensionName_YourDimensionNameCK ON DimYourDimensionName(YourDimensionNameCK);
GO
```

#### Junk Dimension Tables
- Junk dimensions are collections of low cardinality values. 
- The table represents all possible combinations of those values or, at least, all possible combinations of those values that have been imported so far.
- The junk dimension template is almost identical to the dimension table template with one key difference. 
- There is an additional column called `RowHash` which is a computed column that consist of a hash of all the the non-key, non-audit columns.

```sql
USE YourEDW
 
DROP TABLE IF EXISTS [dw].[DimYourDimensionName]
GO
​
SET ANSI_NULLS ON
GO
​
SET QUOTED_IDENTIFIER ON
GO
​
​
CREATE TABLE [dw].[DimYourDimensionName](
[YourDimensionNameCK] [bigint] IDENTITY(1,1) NOT NULL,
--your columns.
[CreatedBy] [nvarchar](50) NULL,
[CreatedOn] [datetime] NULL,
[UpdatedBy] [nvarchar](50) NULL,
[UpdatedOn] [datetime] NULL,
[SourceSystem] [nvarchar](100) NULL,
[SourceSystemKey] [nvarchar](100) NULL,
[EffectiveFrom] [datetime] NULL,
[EffectiveTo] [datetime] NULL,
[IsMostRecentRecord] [bit] NULL,
[RowHash]  AS (CONVERT([binary](16),hashbytes('MD5',concat(
CONVERT([nvarchar](35),Column1,0),
CONVERT([nvarchar](35),Column2,0),
CONVERT([nvarchar](35),Column3,0),
CONVERT([nvarchar](35),Column4,0),
CONVERT([nvarchar](35),Column5,0),
CONVERT([nvarchar](35),Column6,0))),0)) PERSISTED,
 CONSTRAINT [PK_YourDimensionName] PRIMARY KEY CLUSTERED 
(
[YourDimensionNameCK] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
END
​
--index is optional
DROP INDEX IF EXISTS [NCIDX_FactYourFactTableName_RowHash] ON [dw].[FactYourFactTableName]
GO
​
​
CREATE NONCLUSTERED INDEX [NCIDX_FactYourFactTableName_RowHash] ON [dw].[FactYourFactTableName]
(
	[RowHash] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON, OPTIMIZE_FOR_SEQUENTIAL_KEY = OFF) ON [PRIMARY]
GO
```


#####   Standard Columns
-   `CreatedBy: nvarchar(50)`
    -   Who created the record.
    -   Any kind of record creation process should include the code: `CreatedBy = SYSTEM_USER`
-   `CreatedOn: datetime`
    - When the record was created.
    - Any kind of record creation process should include the code: `CreatedOn= CURRENT_TIMESTAMP`
- `UpdatedBy: nvarchar(50)`
    - Who updated the record.
    - If there are any update statements ran against the table, this column should be populated as part of that process. This is especially important when engineers have to make manual adjustments to data. 
    - Your SQL UPDATE statement should include: `UpdatedBy = SYSTEM_USER`
- `UpdatedOn: datetime`
    - When the record was updated.
    - If there are any update statements ran against the table, this column should be populated as part of that process. This is especially important when engineers have to make manual adjustments to data. 
    - Your SQL UPDATE statement should include: `UpdatedOn = CURRENT_TIMESTAMP`
- `SourceSystem: nvarchar(100)`
    - The source system where the data came from. This value is populated from a column of the same name in the staging table.
- `SourceSystemKey: nvarchar(100)`
    - The primary key of the record from the source system.
    - Strictly speaking, this does not have to be the primary key of a source table. It does not even have to be an atomic value. It can be concatenated value. 
    - **The important thing is that the value uniquely identify the row, and that row can be tracked back to a record in the source system.**
- `EffectiveFrom: datetime`
    - If the processing of the dimension requires that history be kept, this column indicates when the row began to represent the most recent version of the data.
    - Usually shares a value with `CreatedOn` 
- `EffectiveTo: datetime`
    - If the processing of the dimension requires that history be kept, this column indicates when the row ceased to represent the most recent version of the data.
    - `IsMostRecentRecord = 1`: the value of `EffectiveTo` is the highest date in the date dimension.
    - `IsMostRecentRecord = 0`, then the value of this column is set to the day it stopped being true. 
    - This has some challenges if there's an intraday change.
        - My solution was to begin retiring records by decrementing a minute amount of time. That is the reason the data type is datatime and not just date. I will illustrate this in the section on loading a Type II SCD.
- `IsMostRecentRecord: bit`
  - Indicates if row represents the most up to date version of that data.

- `RowHash: binary(16)`
  - A computed value of those columns used to compute the uniqueness of a row. This value is used to determine `INSERT` or `UPDATE` operations on the table.


#### Master Data Management Tables
- MDM tables are essentially repurposed junk dimensions. We will talk about the mechanics of how MDM tables work in a later section.
- Regardless of what their data type is in the source system, values should be stored as NVARCHAR so they can be hashed without complaint.
- You'll notice that the primary key does not start at 1. You do not want an enterprise key that is that simple. Throw some zeros on it and make it a real company.

```sql
USE ODS
GO
​
DROP INDEX IF EXISTS [NCIDX_YourDimensionNameMasterData_RowHash] ON [mdm].[YourDimensionNameMasterData]
GO
​
SET ARITHABORT ON
SET CONCAT_NULL_YIELDS_NULL ON
SET QUOTED_IDENTIFIER ON
SET ANSI_NULLS ON
SET ANSI_PADDING ON
SET ANSI_WARNINGS ON
SET NUMERIC_ROUNDABORT OFF
GO
​
DROP TABLE IF EXISTS [mdm].[YourDimensionNameMasterData]
GO
​
​
SET ANSI_NULLS ON
GO
​
SET QUOTED_IDENTIFIER ON
GO
​
CREATE TABLE [mdm].[YourDimensionNameMasterData](
	[YourDimensionNameEK] [bigint] IDENTITY(1000,1) NOT NULL,
	[SourceSystemKey1] [nvarchar](50) NOT NULL,
	[SourceSystemKey2] [nvarchar](50) NOT NULL,
	[RowHash]  AS (CONVERT([binary](35),hashbytes('SHA1',concat(
	CONVERT([nvarchar](35),[SourceSystemKey1],(0)),
	CONVERT([nvarchar](35),[SourceSystemKey2],(0))
	)),(0))) PERSISTED,
 CONSTRAINT [PK_YourDimensionNameMasterData] PRIMARY KEY CLUSTERED 
(
	[YourDimensionNameEK] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON, OPTIMIZE_FOR_SEQUENTIAL_KEY = OFF) ON [PRIMARY]
) ON [PRIMARY]
GO
​
​
​
​
CREATE UNIQUE NONCLUSTERED INDEX [NCIDX_YourDimensionNameMasterData_RowHash] ON [mdm].[YourDimensionNameMasterData]
(
	[RowHash] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, IGNORE_DUP_KEY = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON, OPTIMIZE_FOR_SEQUENTIAL_KEY = OFF) ON [PRIMARY]
GO
```

##### Standard Columns

- `SourceSystemN: NVARCHAR(N)`
    - Holds one of the primary keys that make up the set of keys that uniquely define a complete master data record.
- `RowHash: BINARY(16)`
    - A computed value of those columns used to compute the uniqueness of a row. This value is used to determine `INSERT` or `UPDATE` operations on the table.

#### Stage Tables
- To make life easy, all audit columns in stage tables have default values. The only thing you need to change is the default value for `SourceSystem`.

```sql

USE ODS
​
DROP TABLE IF EXISTS YourSchemaName.YourStageTableNameData
GO
​
​
SET ANSI_NULLS ON
GO
​
SET QUOTED_IDENTIFIER ON
GO
​
​
CREATE TABLE YourSchemaName.YourStageTableNameData(
[ETLKey] [uniqueidentifier] NOT NULL,
[UniqueDims] [varbinary](35) NULL,
[UniqueRows] [varbinary](16) NULL,
[SourceSystem] [nvarchar](255) NULL,
[Cleansed] [bit] NULL,
[ErrorRecord] [bit] NULL,
[ErrorReason] [nvarchar](255) NULL,
[Processed] [bit] NULL,
[RunDate] [datetime] NULL,
 CONSTRAINT [PK_YourStageTableNameData] PRIMARY KEY CLUSTERED 
(
       [ETLKey] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
​
GO
​
ALTER TABLE [YourSchemaName].[YourStageTableNameData] ADD  CONSTRAINT [DF_YourStageTableNameData_ETLKey]  DEFAULT (newid()) FOR [ETLKey]
GO
​
ALTER TABLE [YourSchemaName].[YourStageTableNameData] ADD  CONSTRAINT [DF_YourStageTableNameData_SourceSystem]  DEFAULT (N'DatabaseName') FOR [SourceSystem]
GO
​
ALTER TABLE [YourSchemaName].[YourStageTableNameData] ADD  CONSTRAINT [DF_YourStageTableNameData_Cleansed]  DEFAULT ((0)) FOR [Cleansed]
GO
​
ALTER TABLE [YourSchemaName].[YourStageTableNameData] ADD  CONSTRAINT [DF_YourStageTableNameData_ErrorRecord]  DEFAULT ((0)) FOR [ErrorRecord]
GO
​
ALTER TABLE [YourSchemaName].[YourStageTableNameData] ADD  CONSTRAINT [DF_YourStageTableNameData_Processed]  DEFAULT ((0)) FOR [Processed]
GO
​
ALTER TABLE [YourSchemaName].[YourStageTableNameData] ADD  CONSTRAINT [DF_YourStageTableNameData_RunDate]  DEFAULT (getdate()) FOR [RunDate]
GO
```
​
##### Standard Columns​

- `ETLKey: uniqueidentifier`
    - The primary key of staging tables uses a GUID as a key as opposed to an auto incrementing number. 
    - Using a GUID ensures uniqueness across all stage tables and comes in very handy when tracking the provenance of records. There can be no mistake that a certain record came from a certain table.
- `UniqueDims: varbinary(35)`
    - The primary key of a fact table is the unique combination of all the dimensions attached to that fact table including any degenerate dimensions. 
    - The values of the foreign keys and degenerate dimensions can be hashed to provide a single atomic value for efficiently identifying a specific row in a fact table. 
    - Used in combination with `ETLKey`, this column is used to determine if a record in a stage table made it to the fact table ok. 
- `UniqueRows: varbinary(16)`
    - There are rare instances that arise, usually as a result of a quirk of the source system, where uniqueness can't be determined by the dimensions of the measures and the measures themselves have to be involved in the determination of uniqueness. 
    - In those cases, UniqueRows holds a hash of those measures that are either required to determine uniqueness or used to detect change in a value.
    - When this happens, UniqueDims and UniqueRows is used to determine if a record made it to the fact table ok.
- `SourceSystem: nvarchar(255)`: 
    - The system where the data came from. 
    - This value should be short, plain, and obvious. for e.g. BankOfAmerica. 
    - You want to Pascal case it for when the value winds up in code as is the case when you have master data and need to filter on source system.
- `Cleansed:bit`: Indicates if the data set has been cleansed.
- `ErrorRecord: bit`:  Indicates if this record errored out.
- `ErrorReason: nvarchar(255)`: for storing error messsages to provide diagnostic information.
- `Processed: bit`: Indicates if this record made it to the fact table.
- `RunDate: datetime`: The time stamp the record was loaded.
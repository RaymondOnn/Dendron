---
id: bs3p7ohr9x930swr51mri7x
title: delta_log
desc: ''
updated: 1709041829428
created: 1666439190773
---

##### Documentation 
- <a href="https://databricks.com/blog/2019/08/21/diving-into-delta-lake-unpacking-the-transaction-log.html" target="_blank">Transaction Log</a> 


### Understand the Transaction Log
We can see how Delta stores the different state partitions in separate directories.

Additionally, we can also see a directory called **`_delta_log`**, which is the transaction log.

When a Delta Lake dataset is created, its transaction log is automatically created in the **`_delta_log`** subdirectory.

```py
display(dbutils.fs.ls(delta_path))
```
 
When changes are made to that table, these changes are recorded as ordered, atomic commits in the transaction log.

Each commit is written out as a JSON file, starting with 00000000000000000000.json.

Additional changes to the table generate subsequent JSON files in ascending numerical order.

<div style="img align: center; line-height: 0; padding-top: 9px;">
  <img src="https://user-images.githubusercontent.com/20408077/87174138-609fe600-c29c-11ea-90cc-84df0c1357f1.png" width="500"/>
</div>

```py
display(dbutils.fs.ls(f"{delta_path}/_delta_log/"))
```

 Next, let's take a look at a transaction log File.


The <a href="https://docs.databricks.com/delta/delta-utility.html" target="_blank">four columns</a> each represent a different part of the very first commit to the Delta Table, creating the table.
- The **`add`** column has statistics about the DataFrame as a whole and individual columns.
- The **`commitInfo`** column has useful information about what the operation was (WRITE or READ) and who executed the operation.
- The **`metaData`** column contains information about the column schema.
- The **`protocol`** version contains information about the minimum Delta version necessary to either write or read to this Delta Table.

```py
display(spark.read.json(f"{delta_path}/_delta_log/00000000000000000000.json"))
```

 One key difference between these two transaction logs is the size of the JSON file, this file has 206 rows compared to the previous 7.

To understand why, let's take a look at the **`commitInfo`** column. We can see that in the **`operationParameters`** section, **`partitionBy`** has been filled in by the **`state`** column. Furthermore, if we look at the add section on row 3, we can see that a new section called **`partitionValues`** has appeared. As we saw above, Delta stores partitions separately in memory, however, it stores information about these partitions in the same transaction log file.

```py
display(spark.read.json(f"{delta_path}/_delta_log/00000000000000000001.json"))
```

 Finally, let's take a look at the files inside one of the state partitions. The files inside corresponds to the partition commit (file 01) in the _delta_log directory.

```py
display(dbutils.fs.ls(f"{delta_path}/state=CA/"))
```

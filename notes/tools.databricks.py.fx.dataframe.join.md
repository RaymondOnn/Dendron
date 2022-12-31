---
id: 63fr3bq8iamo9rpppc38vox
title: Join
desc: ''
updated: 1666334844254
created: 1666334844254
---
### Joining DataFrames
The DataFrame <a href="https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.sql.DataFrame.join.html?highlight=join#pyspark.sql.DataFrame.join" target="_blank">**`join`**</a> method joins two DataFrames based on a given join expression. 

Several different types of joins are supported:

Inner join based on equal values of a shared column called "name" (i.e., an equi join)<br/>
**`df1.join(df2, "name")`**

Inner join based on equal values of the shared columns called "name" and "age"<br/>
**`df1.join(df2, ["name", "age"])`**

Full outer join based on equal values of a shared column called "name"<br/>
**`df1.join(df2, "name", "outer")`**

Left outer join based on an explicit column expression<br/>
**`df1.join(df2, df1["customer_name"] == df2["account_name"], "left_outer")`**

We'll load in our users data to join with our gmail_accounts from above.

```python
users_df = spark.read.format("delta").load(DA.paths.users)
display(users_df)

joined_df = gmail_accounts.join(other=users_df, on='email', how = "inner")
display(joined_df)

```
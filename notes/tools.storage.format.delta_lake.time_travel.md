---
id: 3z5bduh1hys4y7o68uco8et
title: Time_travel
desc: ''
updated: 1666439342188
created: 1666439114619
---

##### Documentation
- <a href="https://databricks.com/blog/2019/02/04/introducing-delta-time-travel-for-large-scale-data-lakes.html" target="_blank">Time Travel</a> 



### Access previous versions of table using Time  Travel

 Oops, it turns out we actually we need the entire dataset! You can access a previous version of your Delta Table using Time Travel. Use the following two cells to access your version history. Delta Lake will keep a 30 day version history by default, but if necessary, Delta can store a version history for longer.

```py
spark.sql("DROP TABLE IF EXISTS train_delta")
spark.sql(f"CREATE TABLE train_delta USING DELTA LOCATION '{delta_path}'")
```

```sql
%sql
DESCRIBE HISTORY train_delta
```

 Using the **`versionAsOf`** option allows you to easily access previous versions of our Delta Table.

```py
df = spark.read.format("delta").option("versionAsOf", 0).load(delta_path)
display(df)
```

 You can also access older versions using a timestamp.

Replace the timestamp string with the information from your version history. 

<img src="https://files.training.databricks.com/images/icon_note_32.png"> Note: You can use a date without the time information if necessary.


# TODO
```py
time_stamp_string = <FILL_IN>
df = spark.read.format("delta").option("timestampAsOf", time_stamp_string).load(delta_path)
display(df)
```
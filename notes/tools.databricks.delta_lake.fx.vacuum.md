---
id: kialm70t9lqdhzd943chvmk
title: Vacuum
desc: ''
updated: 1666439298820
created: 1666439263919
---
 ### Vacuum 

Now that we're happy with our Delta Table, we can clean up our directory using **`VACUUM`**. Vacuum accepts a retention period in hours as an input.


 It looks like our code doesn't run! By default, to prevent accidentally vacuuming recent commits, Delta Lake will not let users vacuum a period under 7 days or 168 hours. Once vacuumed, you cannot return to a prior commit through time travel, only your most recent Delta Table will be saved.

```py
from delta.tables import *

delta_table = DeltaTable.forPath(spark, delta_path)
delta_table.vacuum(0)
``` 

 We can workaround this by setting a spark configuration that will bypass the default retention period check.

```py
from delta.tables import *

spark.conf.set("spark.databricks.delta.retentionDurationCheck.enabled", "false")
delta_table = DeltaTable.forPath(spark, delta_path)
delta_table.vacuum(0)
```


 Let's take a look at our Delta Table files now. After vacuuming, the directory only holds the partition of our most recent Delta Table commit.

```py
display(dbutils.fs.ls(delta_path + "/state=CA/"))
``` 

 Since vacuuming deletes files referenced by the Delta Table, we can no longer access past versions. 

The code below should throw an error.

Uncomment it and give it a try.

```py
df = spark.read.format("delta").option("versionAsOf", 0).load(delta_path)
display(df)
```
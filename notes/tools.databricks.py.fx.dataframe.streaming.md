---
id: ew1ywgth871hoi59dyjx464
title: Streaming
desc: ''
updated: 1666437589835
created: 1666437283509
---

 ### Build streaming DataFrames 
Obtain an initial streaming DataFrame from a Delta-format file source.

```py
df = (spark
      .readStream
      .option("maxFilesPerTrigger", 1)
      .format("delta")
      .load(DA.paths.events)
     )

df.isStreaming
```

Apply some transformations, producing new streaming DataFrames.

```py
from pyspark.sql.functions import col, approx_count_distinct, count

email_traffic_df = (df
                    .filter(col("traffic_source") == "email")
                    .withColumn("mobile", col("device").isin(["iOS", "Android"]))
                    .select("user_id", "event_timestamp", "mobile")
                   )

email_traffic_df.isStreaming
```

### Write streaming query results

Take the final streaming DataFrame (our result table) and write it to a file sink in "append" mode.

```py
checkpoint_path = f"{DA.paths.checkpoints}/email_traffic"
output_path = f"{DA.paths.working_dir}/email_traffic/output"

devices_query = (email_traffic_df
                 .writeStream
                 .outputMode("append")
                 .format("delta")
                 .queryName("email_traffic")
                 .trigger(processingTime="1 second")
                 .option("checkpointLocation", checkpoint_path)
                 .start(output_path)
                )
```

### Monitor streaming query
Use the streaming query "handle" to monitor and control it.

```py
devices_query.id

devices_query.status

devices_query.lastProgress

import time
# Run for 10 more seconds
time.sleep(10) 

devices_query.stop()

devices_query.awaitTermination()

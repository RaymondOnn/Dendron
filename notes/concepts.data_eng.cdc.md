---
id: 22qkqgw2r9szkzar4a0zcbo
title: change_data_capture
desc: ''
updated: 1697359627996
created: 1697359565341
---

https://medium.com/@venkatkarthick15/change-data-capture-cdc-3a076c9bdaa3
https://medium.com/@venkatkarthick15/simplify-cdc-with-delta-lakes-change-data-feed-cdf-2ddfdedacf05

### Considerations for CDC

- Scale
  - The CDC pipeline has to be robust enough for high data volume. For example, in PostgreSQL, delays in reading the WAL can cause the database’s disk space to run out!
- Replication lag
  - This refers to the duration between the time that a transaction is committed in the primary database and the time it becomes available in the data warehouse. You have to build checks to make sure that your pipeline is minimizing lag time before transformations are run.
- Schema changes
  - Over time, database schemas evolve because tables or columns are added or removed or types are updated. It is important to propagate the schema changes to the data warehouse. Sometimes a schema change might require a historical sync.
- Masking
  - You have to mask sensitive columns for compliance purposes.
- Historical syncs
  - Before applying the CDC changes, an initial historical sync of the tables is needed. It can take a while and can overload the source. It’s better to do historical syncs from a replica database to speed them up and reduce load on the primary database. Sometimes partial interruptions might occur in the WAL, so you need partial historical syncs instead of full historical syncs to recover fast
---
id: lj5qbm7qo2rlwfmwft7tdl6
title: data_eng
desc: ''
updated: 1697130224102
created: 1695434860642
---

-[Design patterns every data engineer should know](https://rspacesamuel.medium.com/design-patterns-every-data-engineer-should-know-f6c48cd73592)

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
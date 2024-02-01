---
id: 8e750posq9ge4dyuruba8y4
title: Deploy
desc: ""
updated: 1706522451056
created: 1706140383210
---

### [What distinguishes production-grade data pipeline from amateur setups](https://www.reddit.com/r/dataengineering/comments/1addsj4/what_distinguishes_productiongrade_data_pipelines/)

1. handle late-arriving data
2. keep raw data to support reprocessing
3. handle occasional bursts from reprocessing, backfilling, etc
4. support comprehensive unit-testing and constraint-checking
5. minimize tight-coupling by subscribing to domain objects rather than replicate physical schemas
   - Yeah, it's popular when getting a feed from an internal system to just replicate their physical data model into your ETL server or database and then transform the data from there. At the moment, this is often fivetran replicating a database into snowflake/bigquery/redshift to then denormalize, etc via sql in dbt.
   - When this pattern emerged in the early 90s it was often necessary as the only way to get the data, and we had less well-developed software engineering concepts.
   - But what we've known for decades now is that this tightly couples the downstream warehouse to the upstream system's physical model. The results are that the upstream system makes changes and breaks the data warehouse, or the upstream system has to get the warehouse to approve their changes. But they're both bad outcomes.
   - The better approach is for the upstream system to publish a denormalized domain object to kafka, kinesis, s3 files, or even a dedicated table. This domain object is basically all the related fields associated with that domain, like customer, invoice, shipment, etc.
   - That domain schema is then typically locked down with a data contract - enforced by jsonschema. And both the producer and subscriber use it as part of their testing.
6. manage costs on cloud databases
7. provide a low-enough data latency to satisfy their users
8. automate everything
9. observability & alerting & KPIs, logging
10. data dictionaries / data catalogs / documentation
11. error recovery

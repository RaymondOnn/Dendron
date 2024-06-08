---
id: 8e750posq9ge4dyuruba8y4
title: Deploy
desc: ''
updated: 1716386539953
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



Your data sets should spark joy!

How do you do that?
- your data sets are easy to find 
- your data sets have nice column names 
- your data sets have quality guarantees 
- your data sets have freshness guarantees 
- your data sets are generated quickly to provide business value 
- your data sets have key metrics to summarize them 
- your data sets have documentation and diagrams 
- your data sets have lineage explaining where they came from 

No data engineering team does all of these things well! 
Facebook focused on pipeline dev speed, freshness guarantees and discovrability. 
Airbnb focused on quality and documentation at the expense of development speed! 

A data pipeline is more than a regularly refreshing SQL query! 

Pipelines should be resilient along many dimensions: 

- pipelines shouldn’t break when data distribution changes 
Gracefully managing skew is an important part of this journey. 

- pipelines shouldn’t break when ownership changes 
Documented pipelines have much longer lifetimes than undocumented ones!

- pipelines should be able to be updated with new fields and increased requirements 
Adding the proper staging steps and upstream combinations will allow for easier change management of your pipeline. 

- pipelines shouldn’t publish bad data!
Bad data should be caught before it’s published. Write-audit-publish pattern is the best way to solve this! 
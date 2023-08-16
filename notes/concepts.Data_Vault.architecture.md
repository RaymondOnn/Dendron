---
id: wtbzy7g3e88slpk9cvig0cc
title: architecture
desc: ''
updated: 1689841052506
created: 1689840971074
---
# Architecture 
- Config (code) driven by passing variable tht determines the behaviour of the platform
- Allows for versioning of the infrastructure itself
- Every component is decoupled, automated and repeatable and promotes exploration through discovery and machine learning
- Resembles a (loosely coupled) micro-services architecture that allows for independent component maintenance and avoids vendor lock in

### Schema-on-read VS schema-on-write
- Schema-on-write:
    - known as structured data e.g. csv
    - Every column is profile before ingesting
    - Data structure and data type for each column is known beforehand
- Schema-on-read:
    - known as semi-structured data e.g. parquet, avro, json
    - Data arrives schema-less as text/string and a schena us defined upon reading the data
    - Advantage here is you only need to define the schema for the portion of data needed.

(skipped)
## Data Storage: The way data is stored
- Relational, structured, SQL
    - Relationshps between tables are inferred through use of foreign keys to primary key constraints

- Relational, graph, NoSQL
- Semi-structed, SQL, NoSQL
- Column Families, NoSQL

### ACID and BASE
- ACID:
    - Atomicity: Each transaction either completely succeeds or completely fails
    - Consistncy: Data meets all defined rules for constraints, cascading and triggers
    - Isolation: Concurrent execution safe
    - Durability: Commited transactions are persisted
- BASE:
    - Basically, Available
    - Soft
    - Eventually    

## Data Ingestion
### The General Process
1. Data from all sources are posted to a landing zone to be ingested into the data lake/warehouse
2. A service level agreement is establised between the supplier and consumer with an expectation on
    - what protocols to use
    - what format the data should come in
    - what to do when faults ocur
    - how frequent the data arrives in the landing zone
3. The landing zone can act as a data lake for skilled users to have access to data. However, data governance required to ensure data is categorized,identified and not misused.
4. Data is loaded into data warehouse and data marts, Data is also sent to be backed up


### Master Data Management
- Master data refers to data which are agreed on and shared across the enterprise e.g customer, employee, product data
- Harmonise business keys
- As the enterprise grows, the need for trusted data becomes more critical
- Here, data is harmonized across various systems to create the golden record and enables a single source of facts
- Done through data cleansing, standardizing and match merging data
- After harmonizing the data, MDM assigns an internal global id that is persisted to the source systems

### Reference Data Management
- https://www.youtube.com/watch?v=SkZCQ6KZfi0&ab_channel=GlobalDataStoreLLC
- Reference data is a subset of master data that defined the set of permissible values to be used by other data fields e.g. country codes, industry classification
- Harmonizes different ways of expressions about the same object / entity
- Basically a lookup table that ensure consistency in how an object is expressed 


## Staging
- Purpose of staging is to prepare the data for ingestion
- the place to do any 
    - last minute transformation
    - tokenization of personal identifiable info
    - application of hard business rule to maintain contistency and prevent failed loads
    - data quality testing via rules 
    - Data vaults metadata tags are prepared and added to staging content
        - files that are landed and staged must not have any dependency on any other table to prevent staggered loads
        - No lookups as well!
- Once loaded into data vault, the data can be moved/stored elsewhere (to inactive storage, and then to cold storage) where life cycle management of data object takes over

## Data Provisioning
- Here, data is properly shaped for downstream application if required
- Ideally, data here should come from the central version of the data warehouse
- For real-time data, data should from straight from ognestion, but with a copy kept in the data lake for audit purposes
- The consumers of data
    - Business Intelligence
    - Advanced Analytics
    - Artificial Intelligence
    - API Hubs
    - Write Back?
    - Data Virtualization & Federation ?

## Data Management
- Data Goverence: Risk management via controlling access to data
- Data Catalogs
    - Data dictionaries provide system level description and relationships of data
    - Data catalogs provides a simplied directory and search capability to available datasets
- Data Lineage
- Data Retention
- Regulation
- Redundant Data 
- Data Quality Framework
- Data Logging
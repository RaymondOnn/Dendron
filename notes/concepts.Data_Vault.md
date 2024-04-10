---
id: ktrobmdmdd0p70w7ml9uo5a
title: Data_Vault
desc: ''
updated: 1711791123439
created: 1689743715715
---

https://medium.com/joyn-tech-blog/a-lightweight-data-vault-automation-framework-with-snowflake-ca239ff49d5b
https://github.com/joyn/data-vault-framework
https://patrickcuba.medium.com/data-vault-test-automation-52c0316e8e3a


---

## The Raw Vault
## Hubs
### What are hubs
- Contains a unique list of entities or things that represents a domain or concept within the company
- Acts as the central immutable store, representing business entities
- Everything connected to the hub provides more context about the entity i.e. relationships with other entities, descriptive info about the entity itself
- Will have a unique identifier that forever represent the entity to the business
- As long as of same semantic grain. the business keys from different source systems of the same entity can be loaded to the same hub table

### Table Structure

- `Business Key: text` (mandatory)
  - Used to uniquely represent the entity and created from a source app i.e. contract_id, account_id, customer_id
  - Should not be the surrogate key supplied from source platform but can be used if no unique identifier present
- `Business Key Collision code: text` (required)
  - Used to address hash collision
  - if collision code not needed, set default value instead
- `Multi-tenant id: text` (required)
  - Used to address hash collision
  - if single tenant models, assign a default value instead
- `Surrogate Key` (required if hash-key based)
  - Only used to track info about an entity and doesn't represent anything meaningful i.e. GUID, sequence key 
  - Either a sequence or hash-key representation of the business key
  - One-to-one representation of business key and should never change
  - Sequence keys deprecated in data vault 2.0
  - Inherited by all other tables relating to that business entity
- `Record source: text` (required)
  - The first record source that supplied the business key
- `Applied timestamp: timestamp` (recommended)
  - first timestamp when the business key was supplied
- `load_timestamp: timestamp` (required)
  - first timestamp recorded when the business entity was loaded 
- `task_id: text` (recommended)
  - first task_id that supplied the business entity
- `user_id: text` (optional)
  - first `user_id` that supplied the business entity
- `jira_id: text` (optional)
  - first JIRA task id that supplied the business entity

> NOTE: A ghost record is sometimes inserted upon table creation, especially for schema-on-read platforms

### Hash Collision prevention strategies


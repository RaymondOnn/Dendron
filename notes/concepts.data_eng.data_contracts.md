---
id: e9c7ldz7yp9eklhlmbigvml
title: data_contracts
desc: ''
updated: 1715102524908
created: 1697308207094
---
https://medium.com/profitoptics/data-contract-101-568a9adbf9a9
https://medium.com/@tombaeyens/data-contracts-the-missing-foundation-3c7a98544d2a
https://prukalpa.medium.com/gordon-wong-using-a-data-contract-to-build-trust-in-modern-data-teams-ae10cb71b4dc
https://github.com/paypal/data-contract-template
### Data Quality Dimensions


#### Accuracy
Accuracy in data quality refers to the degree to which data correctly describes the “real world” object or event being described. For data to be accurate, it must be correct and free from errors.

#### Validity
Validity in data quality refers to the extent to which the data conforms to defined business rules or constraints. Valid data helps to ensure that the data aligns with the expected format and value ranges, which is crucial for subsequent data processing and analysis.


#### Uniqueness
Uniqueness in the context of data quality refers to the requirement that a data value should be represented only once in a dataset. This is especially important when dealing with identifiers, where duplication might result in incorrect data mapping or aggregation.

#### Completeness
Completeness in data quality refers to the requirement that a particular set of data should be comprehensive, all-inclusive, and contain all the necessary elements. In the context of a dataset, this implies that all the expected data values should be present without any omissions.

#### Consistency
Ensuring consistency in our dataset is an essential aspect of maintaining high data quality. Consistency means that the data across the entire dataset follows a specific format, range, or pattern, providing reliable and predictable data for analysis.

### What a Data Contract should contain:
1. Format and Structure
   - The fields and nested fields that can or should be contained in the data. Which fields should always be present, which are optional
   - The types of the fields in the data:
     - data types like string, boolean, number types and arrays of these
     - whether they are nullable
     - whether they are unique
     - whether they can only take specific values (eg enums and numeric ranges)
     - whether they have to fit a certain format or regex (eg UUIDs)
   - Metadata such as descriptions of the data, its grain, its context and also descriptions of its fields
   - The version of the data: versions of data should be immutable. If the data is changed, then the version is bumped
     - 0.0.1 to 0.0.2 is a small bump for a small change, like adding a field without changing grain
     - 0.0.2 to 0.1.0 is a medium bump for a moderate change that could be breaking, such as removing a field or changing its format
     - 0.1.0 to 1.0.0 for large structural changes to the data, which mean the consumption method has to change materially
   - Does the data contain sensitive information, and in which fields?
   - Standards around structure (eg no arrays in the middle of the structure please)
   - Standards around which fields are required (eg if this is a product clicked event, there should be fields about the product included)
   - Environment, eg Test, Dev, Prod

2. SLAs and Owners
   - When and only when should this data be sent
   - Timelines
   - Bifurcation (splitting where data that meets the Contract, and that which doesn’t, goes respectively and is treated)
   - Who is the producer of the data
   - Who is responsible for the data
   - Who is accountable for the data
   - Who should be consulted about the data
   - Who should be informed about the data
   - How long will old versions of the data be available
   - When should the data expire

3. Semantics
   - Which entities are at play in this data
   - Is this data about an entity or an event (all data is about entities, but not all data are events)
   - Which metrics can be calculated from this data and how (eg in order state change events: revenue = sum(case when state = ‘completed’ then order_amount end) - sum(case when state = ‘refunded’ then order_amount end)
   - How does the data relate to other data (eg foreign keys)


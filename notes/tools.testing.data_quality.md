---
id: lpq84dtr3fuajtzbeyxoiri
title: Data_quality
desc: ''
updated: 1726843434762
created: 1717010665373
---


Netflix WAP Pattern: <https://www.youtube.com/watch?v=fXHdeBnpXrg>
Netflix Metric Shifts: <https://www.youtube.com/watch?v=C3sbxtRe2Po>
Netfliix Design for Failure: <https://www.youtube.com/watch?v=nMyuCdqzpZc>

Data Quality Checks:

- Total Row Count: Check if extra rows were accidently created
- NULL Count: Check if joins work properly
- Duplicate Check / Unique Key Count: Check for duplicate rows. Primary key columns must always be distinct
- Consistency Check: Ensuring consistency of data across multiple sources (e.g., data in one system should match that in another if they are synchronized).
- Validity Check: Validating that data conforms to predefined formats or domains (e.g., email addresses, date formats, phone numbers, etc.).
- Data Type Validation: Checking that values are stored in the appropriate data types (e.g., numbers in numeric columns, dates in date columns).
- Cross-field Validation: Ensuring that relationships between fields are logical (e.g., end date should be greater than or equal to start date).

---
id: jnw0q19p58x2qbsssilakdt
title: Data_sampling
desc: ''
updated: 1666055052777
created: 1666055046917
---


# Data Sampling #
- Queries on very large tables can take a lot of time and compute resources
- This is not ideal for doing testing & development, since it can be very costly
- Data sampling allows us get a sample of the large table to do testing and development work on. 
- Execution of queries are faster and cheaper on the sample

## Types  ##
- ROW / BERNOULLI method
    - Every row is chosen with percentage p
    - More random -> more representative
    - More suitable for smaller tables
- BLOCK / SYSTEM method
    - Every block is chosen with percentage p
    - More effective/efficient processing (recommended by snowflake)
    - Better for larger tables

## Implementation ##

```sql
-- Setup
CREATE OR REPLACE TRANSIENT DATABASE SAMPLING_DB;


CREATE OR REPLACE VIEW ADDRESS_SAMPLE
AS SELECT * 
-- Actual table has 32.5M rows
FROM SNOWFLAKE_SAMPLE_DATE.TPCOS_SF10TCL.CUSTOMER_ADDRESS 
-- Using ROW method to get 1% of data; SEED for reproducibility
SAMPLE ROW (1) SEED(26) 

-- SAMPLE has 3248622 rows
SELECT * FROM ADDRESS_SAMPLE


SELECT CA_LOCATION_TYPE, COUNT(*)/3248622*100
FROM ADDRESS_SAMPLE
GROUP BY CA_LOCATION_TYPE

SELECT * 
FROM SNOWFLAKE_SAMPLE_DATE.TPCOS_SF10TCL.CUSTOMER_ADDRESS
-- Using SYSTEM method to get 1% of every micro partition; SEED for reproducibility
-- Generally, row count returned will not be as accurate
SAMPLE SYSTEM (1) SEED (26)

-- SAMPLE has 3900000 rows
SELECT * FROM ADDRESS_SAMPLE

```

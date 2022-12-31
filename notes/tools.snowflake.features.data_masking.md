---
id: cjy81vk7pi3eapo0szeorbt
title: Data_masking
desc: ''
updated: 1666056775437
created: 1666056775437
---

## Data Masking ##
- a type of Column-Level Security where masking is applied to control who can see the data

## Implementation ##

```sql

-- Set Up
USE DEMO_DB;
USE ROLE ACCOUNTADMIN;

-- Set Up Roles
CREATE OR REPLACE ROLE ANALYST_MASKED;
CREATE OR REPLACE ROLE ANALYST_FULL;

-- Grant select on table to Roles
GRANT SELECT ON TABLE DEMO_DB.PUBLIC.CUSTOMERS TO ROLE ANALYST_MASKED;
GRANT SELECT ON TABLE DEMO_DB.PUBLIC.CUSTOMERS TO ROLE ANALYST_FULL;

GRANT USAGE ON SCHEMA DEMO_DB.PUBLIC.CUSTOMERS TO ROLE ANALYST_MASKED;
GRANT USAGE ON SCHEMA DEMO_DB.PUBLIC.CUSTOMERS TO ROLE ANALYST_FULL;

-- Grant Warehouse access
GRANT USAGE ON WAREHOUSE COMPUTE_WH TO ROLE ANALYST_MASKED;
GRANT USAGE ON WAREHOUSE COMPUTE_WH TO ROLE ANALYST_FULL;

-- Assign Roles to User
GRANT ROLE ANALYST_MASKED TO USER NIKOLAISCHULER;
GRANT ROLE ANALYST_FULL TO USER NIKOLAISCHULER;

-- Set Up Masking policy
CREATE OR REPLACE MASKING POLICY PHONE
AS (val varchar) returns varchar ->   -- data types must be same i.e. varchar returns varchar
    CASE
        WHEN current_role() IN ('ANALYST_FULL', 'ACCOUNTADMIN') THEN val
        ELSE '##-###-##'
    END;

-- Apply policy on a specific column
ALTER TABLE IF EXISTS CUSTOMERS MODIFY COLUMN PHONE
SET MASKING POLICY PHONE;

-- Validating policies
USE ROLE ANALYST_FULL;
SELECT * FROM CUSTOMERS;

USE ROLE ANALYST_MASKED;
SELECT * FROM CUSTOMERS;

```

## Unset and Replace Policy ##

```sql
USE ROLE ACCOUNTADMIN;

-- 1. Apply policy to multiple columns
-- Apply to specific column
ALTER TABLE IF EXISTS CUSTOMERS MODIFY COLUMN FULL_NAME
SET MASKING POLICY PHONE

-- 2. Replace or Drop Policy

-- NOTE: THESE COMMANDS WON'T WORK
-- Masking policy cannot be dropped/replace when associated with one or more entities
DROP MASKING POLICY phone

CREATE OR REPLACE MASKING POLICY PHONE
AS (val varchar) returns varchar ->   -- data types must be same i.e. varchar returns varchar
    CASE
        WHEN current_role() IN ('ANALYST_FULL', 'ACCOUNTADMIN') THEN val
        ELSE CONCAT(LEFT(val, 2), '*******')
    END;

-- List and Describe Policies
DESC MASKING POLICY names;
SHOW MASKING POLICIES;

-- Show columns with applied policies
SELECT * FROM TABLE(INFORMATION_SCHEMA.POLICY_REFERENCES(POLICY_NAME=>'phone'));

-- SOLUTION: Unset masking policy first 
ALTER TABLE IF EXISTS CUSTOMERS MODIFY COLUMN full_name
UNSET MASKING POLICY;

ALTER TABLE IF EXISTS CUSTOMERS MODIFY COLUMN phone
UNSET MASKING POLICY;

```

## Alter an Existing Policy ##

```SQL

USE ROLE ANALYST_MASKED;

-- dates returns 0001-01-01 00:00:00.000
SELECT * FROM CUSTOMERS;

USE ROLE ACCOUNTADMIN;

ALTER MASKING POLICY dates
SET body ->
    CASE
        WHEN current_role() IN ('ANALYST_FULL') THEN val
        ELSE DATE_FROM_PARTS(1111, 11, 11)::date 
    END;

-- dates now returns 1111-11-11    
```

## More examples ##

```SQL

USE ROLE ACCOUNTADMIN;

-- FROM abc@email.com -> *****@email.com
CREATE OR REPLACE MASKING POLICY emails
AS (val varchar) returns varchar ->  
    CASE
        WHEN current_role() IN ('ANALYST_FULL') THEN val
        ELSE current_role() IN ('ANALYST_MASKED') THEN regexp_replace(val, '.+\@', '*****@') -- leave email domain unmasked
    END;

-- Apply policy on a specific column
ALTER TABLE IF EXISTS CUSTOMERS MODIFY COLUMN email
SET MASKING POLICY email;


-- Same person, same hash 
CREATE OR REPLACE MASKING POLICY sha2
AS (val varchar) returns varchar ->  
    CASE
        WHEN current_role() IN ('ANALYST_FULL') THEN val
        ELSE sha2(val) -- return hash of column value
    END;


CREATE OR REPLACE MASKING POLICY dates
AS (val date) returns date ->  
    CASE
        WHEN current_role() IN ('ANALYST_FULL') THEN val
        ELSE DATE_FROM_PARTS(0001, 01, 01)::date  -- return 0001-01-01 00:00:00.000
    END;


```
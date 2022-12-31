---
id: iptjifxshxi2quhp20qroqc
title: Query_tag
desc: ''
updated: 1670679348896
created: 1670676881922
---

## Query Tags
- Schema-level objects that are be associated to another objects
- A tag can be assigned a string value (up to 2000 characters) when tagging a object
- Stored as a key-value pair `"key = value"`
- An object can have up to 20 tags
- Tags are replicated fro


### Query Tags can be set at various levels: Account, User, Session
<br>

For e.g. If set at the `Session` level, queries within the same    `Session` will be tagged

- Set tags using the `SET QUERY TAG` command
- To undo, `UNSET QUERY TAG` 
```sql
ALTER ACCOUNT ___ SET QUERY_TAG = "...";
ALTER USER ___ SET QUERY_TAG = "...";
ALTER SESSION ___ SET QUERY_TAG = "...";

ALTER ACCOUNT ___ UNSET QUERY_TAG;
ALTER USER ___ UNSET QUERY_TAG;
ALTER SESSION ___ UNSET QUERY_TAG;


-- To Check if tags are set
SHOW PARAMETERS LIKE 'query_tag'; -- session level
SHOW PARAMETERS LIKE 'query_tag' IN USER; -- user level
SHOW PARAMETERS LIKE 'query_tag' IN ACCOUNT; -- account level
```

### Viewing Query Tags
- The tags are displayed in the output of the `QUERY_HISTORY` , `QUERY_HISTORY_BY_*` functions.
- For Session query tags, also available in the History Page

```sql
SELECT * FROM TABLE(INFORMATION_SCHEMA.QUERY_HISTORY()) ORDER BY START_TIME
SELECT * FROM TABLE(INFORMATION_SCHEMA.QUERY_HISTORY_BY_USER()) ORDER BY START_TIME
SELECT * FROM TABLE(INFORMATION_SCHEMA.QUERY_HISTORY_BY_SESSION()) ORDER BY START_TIME
```

### Implementation
- To add more info, we can use a JSON structure
  
```sql
-- set our query tag value
ALTER SESSION SET query_tag = 
 '{
  "PGMID":296282,
  "PGMNM":"item_class",
  "PGMVER": "1.2.123",
  "MODNM":"generate_class",
  "FNCNM":"join_to_item_mstr",
  "STMNM":"item_class_filter_on_active_items",
  "EXECID":"92c6da4e-68de-4ad0-9861-7e72819a02af"
  }';

-- execute our query with the tag associated to it
-- for query history
SELECT * FROM item_master im
INNER JOIN
class_master cm ON 
im.upc = cm.upc;

-- unset the query tag - otherwise this query tag will be used subsequent queries
ALTER SESSION UNSET query_tag;

--check query history for our tagged query
SELECT query_id, query_tag, * 
FROM snowflake.account_usage.query_history
WHERE query_id = '{your query ID here}'

-- query log table
CREATE OR REPLACE TABLE QUERY_LOG 
(
 QUID VARCHAR,
 TAG VARIANT
);

--insert the query ID and query tag into our query_log table
INSERT INTO query_log 
(
  SELECT query_id, parse_json(query_tag) 
  FROM snowflake.account_usage.query_history 
  WHERE query_id = '{your query ID here}'
);

-- review data from query log
SELECT 
 quid
,tag:PGMID
,tag:PGMNM
,tag:PGMVER
,tag:MODNM
,tag:FNCNM
,tag:STMNM
,tag:EXECID
FROM query_log;

-- review data from query log
SELECT 
qh.*
,quid
,tag:PGMID
,tag:PGMNM
,tag:PGMVER
,tag:MODNM
,tag:FNCNM
,tag:STMNM
,tag:EXECID
FROM query_log ql
INNER JOIN 
snowflake.account_usage.query_history qh ON
ql.quid = qh.query_id;
```
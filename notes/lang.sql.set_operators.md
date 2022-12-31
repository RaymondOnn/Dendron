---
id: qf8qbwpgenkvs915m95pir7
title: Set_operators
desc: ''
updated: 1670682039269
created: 1670681484241
---
## SET OPERATORS
* Useful for QA, Troubleshooting
* Both tables need to have the same schema

### **`MINUS / EXCEPT`**: 

```sql
-- Show rows in RAW that are not in RAW_DEV
SELECT * FROM RAW.AVENGERS.AVENGERS_HISTORY

MINUS

SELECT * FROM RAW_DEV.AVENGERS.AVENGERS_HISTORY

--Except
-- Show rows in RAW_DEV that are not in RAW
SELECT * FROM RAW.AVENGERS.AVENGERS_HISTORY

EXCEPT

SELECT * FROM RAW_DEV.AVENGERS.AVENGERS_HISTORY
```
<br>

### **`INTERSECT`**:
```sql
-- Show rows that are in both RAW & RAW_DEV
SELECT * FROM RAW.AVENGERS.AVENGERS_HISTORY

INTERSECT

SELECT * FROM RAW_DEV.AVENGERS.AVENGERS_HISTORY
```
<br>

### **`UNION ALL`**:
- `UNION` will provide a distinct list
```sql
-- Show ros that are in either RAW or RAW_DEV
SELECT * FROM RAW.AVENGERS.AVENGERS_HISTORY

UNION ALL

SELECT * FROM RAW_DEV.AVENGERS.AVENGERS_HISTORY
```
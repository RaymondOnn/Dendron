---
id: xuh20ha1z4ttp92o0j5r8u5
title: variables
desc: ''
updated: 1698446443898
created: 1667181324471
---


### How to use variable?
- Only exists within a session (i.e. worksheet)

```sql
SET team_name = '76ers';

SELECT *
FROM NBA_Players where team = $team_name;
```

### Setting multiple variables

```sql
-- Method 1
SET team_name = 'Heat'
SET first_name = 'Kyria'
SET last_name = 'James'

-- Method 2: More efficient
SET (team_name, first_name, last_name) = ('Heat', 'Kyria', 'James')
```

### Show Variables

```sql
SHOW VARIABLES

SHOW VARIABLES LIKE '%NAME%"
```

### `UNSET` Variables

```sql

UNSET first_name
```
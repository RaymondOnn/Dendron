---
id: mm9s3jcmpsy9efi5wy9xv72
title: transactions
desc: ''
updated: 1727461419109
created: 1727368459641
---

## Transactions

### What is a transaction?

- A transaction is a sequence of SQL statements that are processed as an atomic unit.
- All statements in the transaction are either applied (committed) or undone (rolled back) together.
- Snowflake transactions guarantee ACID properties.
- A transaction can include both reads and writes.
- Transactions follow these rules:
  - Transactions are never nested.
    - For example, you cannot create an outer transaction that would roll back an inner transaction that was committed, or create an outer transaction that would commit an inner transaction that had been rolled back.
  - A transaction is associated with a single session. Multiple sessions cannot share the same transaction.

### Implicit vs Explicit Transactions

#### Implicit Transactions

- By default, all transactions are auto-committed i.e. `AUTOCOMMIT=TRUE`
- This means that each DDL statement executes as a separate transaction.
  - If a DDL statement is executed while a transaction is active, the DDL statement:
    - Implicitly commits the active transaction.
    - Executes the DDL statement as a separate transaction.
  - Because a DDL statement is its own transaction, you cannot roll back a DDL statement; the transaction containing the DDL completes before you can execute an explicit ROLLBACK.
  - If a DDL statement is followed immediately by a DML statement, that DML statement implicitly starts a new transaction.
- Statements are automatically committed if it succeeds, and automatically rolled back if it fails.

#### Explicit Transactions

- To avoid auto-committing, we can execute the statements within a `BEGIN` block

  ``` sql
  BEGIN;

  UPDATE DEMO_DB.DEMO_SCHEMA.SOME_TABLE SET AMOUNT=123;
  
  -- save state
  -- remember to commit else all other transactions that update the same table will be blocked
  COMMIT;  

  ROLLBACK; -- revert back to previous state
  ```

- A transaction can be started explicitly by executing a `BEGIN` statement. Snowflake supports the synonyms `BEGIN WORK` and `BEGIN TRANSACTION`. Snowflake recommends `BEGIN TRANSACTION`.
- A transaction can be ended explicitly by executing `COMMIT` or `ROLLBACK`.
  - `COMMIT WORK` and `COMMIT` are the same thing,
  - `ROLLBACK WORK` is also the same as `ROLLBACK`.

- In general, if a transaction is already active, any `BEGIN TRANSACTION` statements are ignored.
- Users should avoid extra `BEGIN TRANSACTION` statements, however, because they make it much more difficult for human readers to pair up a COMMIT (or ROLLBACK) statement with the corresponding BEGIN TRANSACTION statement.
  - One exception to this rule involves a nested stored procedure call.

### Locking Transactions

- What happens when transactions of the same kind are done on the same table concurrently in separate sessions?
  - `UPDATE`: Locking occurs.
  - `INSERT`: Locking does not occur since the transactions are inserted data into different micropartitions of the table

### Transaction Management

- To see the locks acquired currently by the system

  ```sql
  SHOW LOCKS IN ACCOUNT;
  SELECT SYSTEM$ABORT_TRANSACTION(<transaction_id>)
  ```

- To see parameters set at account level for locking
  - This helps with preventing a transaction spending more time for lock aquisition

  ```sql
  SHOW PARAMTERS;
  SELECT * FROM TABLE(RESULT_SCAN(LAST_QUERY_ID())) WHERE "key" = 'LOCK_TIMEOUT';
  ```

- To check the query history logs

  ```sql
  SELECT * FROM TABLE(SNOWFLAKE.INFORMATION_SCHEMA.QUERY_HISTORY())
  WHERE TRANSACTION_BLOCKED_TIME>0
  ORDER BY start_time DESC
  ```

# SQL Notes
## 1. TCL — Transactions
Group multiple statements so they all succeed or all fail together.
```sql
BEGIN TRANSACTION;

UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;

COMMIT;      -- save changes permanently
-- ROLLBACK; -- undo all changes since BEGIN
```

With error handling (SQL Server)
```sql
BEGIN TRY
    BEGIN TRANSACTION;
    -- statements here
    COMMIT;
END TRY
BEGIN CATCH
    ROLLBACK;
END CATCH
```


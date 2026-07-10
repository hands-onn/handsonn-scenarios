# Watch the constraints bite

Insert one valid account:

```sql
INSERT INTO accounts (email, balance) VALUES ('alice@example.com', 100);
```

Now try to break the rules — both of these should **fail**:

```sql
-- duplicate email → violates UNIQUE
INSERT INTO accounts (email, balance) VALUES ('alice@example.com', 5);

-- negative balance → violates CHECK
INSERT INTO accounts (email, balance) VALUES ('bob@example.com', -10);
```

Postgres rejects each with a clear error (`duplicate key value violates
unique constraint` / `violates check constraint`). The bad rows never
land. Confirm only the one valid row exists:

```sql
SELECT count(*) FROM accounts;   -- 1
```

This is the point: **the data model itself refuses corruption.** You
don't need application code to prevent a negative balance — the database
won't allow it, no matter which client connects.

# Foreign keys — enforce relationships

A **foreign key** guarantees a value references a real row in another
table. Add a `transactions` table tied to `accounts`:

```sql
CREATE TABLE transactions (
  id         SERIAL PRIMARY KEY,
  account_id INT NOT NULL REFERENCES accounts(id) ON DELETE CASCADE,
  amount     NUMERIC(12,2) NOT NULL,
  created_at TIMESTAMPTZ NOT NULL DEFAULT now()
);
```

`REFERENCES accounts(id)` means you can't insert a transaction for an
account that doesn't exist:

```sql
-- fails: no account 999
INSERT INTO transactions (account_id, amount) VALUES (999, 10);

-- works: account 1 (alice) exists
INSERT INTO transactions (account_id, amount) VALUES (1, 10);
```

`ON DELETE CASCADE` means deleting an account automatically deletes its
transactions — no orphaned rows. (Other options: `RESTRICT` to block the
delete, `SET NULL` to null the reference.)

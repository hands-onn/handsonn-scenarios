# Constraints are your first line of defense

Constraints let the database *reject* invalid data before it's ever
stored — far more reliable than hoping every app is bug-free. Create an
`accounts` table that enforces its own rules:

```bash
PGPASSWORD=lab psql -h db -U postgres -d shop
```

```sql
CREATE TABLE accounts (
  id       SERIAL PRIMARY KEY,             -- unique, auto-incrementing
  email    TEXT NOT NULL UNIQUE,           -- required + no duplicates
  balance  NUMERIC(12,2) NOT NULL DEFAULT 0
             CHECK (balance >= 0)          -- can never go negative
);
```

Each clause is a guarantee:
- `PRIMARY KEY` — unique, non-null row identity.
- `NOT NULL` — the column must have a value.
- `UNIQUE` — no two rows share this value.
- `CHECK (...)` — an arbitrary boolean the row must satisfy.
- `DEFAULT` — value used when none is supplied.

Inspect it:

```sql
\d accounts
```

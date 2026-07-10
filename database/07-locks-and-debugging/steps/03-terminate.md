# Clear the block

Two escalating options:

- **`pg_cancel_backend(pid)`** — cancels the current query, leaves the
  connection. Gentler; try first for a runaway query.
- **`pg_terminate_backend(pid)`** — kills the whole connection, rolling
  back its open transaction and releasing all its locks. Needed here,
  since the session is idle (no query to cancel) but holding a lock.

```bash
PID=$(cat /tmp/blocker_pid)
PGPASSWORD=lab psql -h db -U postgres -d shop -c \
  "SELECT pg_terminate_backend($PID)"
```

Confirm the lock is gone — the UPDATE that failed before now succeeds:

```bash
PGPASSWORD=lab psql -h db -U postgres -d shop -c \
  "UPDATE inventory SET qty = qty - 1 WHERE sku = 'A1'"
```

And no session is idle-in-transaction anymore:

```bash
PGPASSWORD=lab psql -h db -U postgres -d shop -tAc \
  "SELECT count(*) FROM pg_stat_activity WHERE state LIKE 'idle in transaction%'"
```

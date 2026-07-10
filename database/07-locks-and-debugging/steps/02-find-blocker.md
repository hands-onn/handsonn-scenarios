# Who holds the lock?

`pg_stat_activity` is the live view of every backend connection.
`pg_blocking_pids()` tells you which backends block a given one. Look at
what's active:

```bash
PGPASSWORD=lab psql -h db -U postgres -d shop
```

```sql
SELECT pid, state, wait_event_type, left(query, 50) AS query
FROM pg_stat_activity
WHERE datname = 'shop' AND pid <> pg_backend_pid();
```

You'll spot a backend stuck in **`idle in transaction`** running a
`pg_sleep` — that's the culprit holding the lock. Its `pid` is what you
need.

Confirm it's actually blocking, using the built-in helper:

```sql
SELECT pid, pg_blocking_pids(pid) AS blocked_by, state
FROM pg_stat_activity
WHERE datname = 'shop';
```

Save the blocker's PID:

```bash
PGPASSWORD=lab psql -h db -U postgres -d shop -tAc \
  "SELECT pid FROM pg_stat_activity
   WHERE datname='shop' AND state LIKE 'idle in transaction%' LIMIT 1" > /tmp/blocker_pid
cat /tmp/blocker_pid
```

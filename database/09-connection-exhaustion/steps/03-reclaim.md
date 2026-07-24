# Reclaim the leaked slots

You can free a slot from the server side with **`pg_terminate_backend(pid)`**
— it closes that backend's connection (rolling back any open transaction
and releasing its slot). Two ways to apply it here.

Terminate one by hand to see it work — grab any idle `app` pid from the
previous step:

```bash
PGPASSWORD=lab psql -h db -U postgres -d shop -c \
  "SELECT pg_terminate_backend(<pid>)"
```

Then sweep the rest in a single statement — feed every idle `app` pid
straight into `pg_terminate_backend`:

```bash
PGPASSWORD=lab psql -h db -U postgres -d shop -c "
  SELECT pg_terminate_backend(pid)
  FROM pg_stat_activity
  WHERE usename = 'app' AND state = 'idle';"
```

Confirm the pool has drained — the idle `app` count should be at (or
near) zero:

```bash
PGPASSWORD=lab psql -h db -U postgres -d shop -c \
  "SELECT count(*), state FROM pg_stat_activity GROUP BY state"
```

You've just done the emergency remediation an operator reaches for when a
leak has locked everyone out. It buys time — the real fix comes next in
the debrief.

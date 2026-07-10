# How does Postgres run your query?

`EXPLAIN` shows the *plan* — how Postgres intends to execute a query
without running it. `EXPLAIN ANALYZE` actually runs it and reports real
timings.

The `events` table has 100,000 rows and no index on `user_id`. See what
happens when you filter on it:

```bash
PGPASSWORD=lab psql -h db -U postgres -d shop
```

```sql
EXPLAIN ANALYZE
SELECT * FROM events WHERE user_id = 42;
```

Look at the top node: **`Seq Scan on events`** — Postgres reads all
100k rows and checks each one, because it has no faster way to find
`user_id = 42`. Note the execution time.

Record the scan type:

```bash
PGPASSWORD=lab psql -h db -U postgres -d shop -tAc \
  "EXPLAIN SELECT * FROM events WHERE user_id = 42" | head -1 > /tmp/plan_before
cat /tmp/plan_before
```

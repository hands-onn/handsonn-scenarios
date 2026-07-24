# Restore and prove it

Now bring the data back from `/tmp/backup.dump`. Because the archive is
custom-format, you replay it with `pg_restore` (not `psql`):

```bash
PGPASSWORD=lab pg_restore -h db -U postgres -d store /tmp/backup.dump
```

`pg_restore` reads the TOC, recreates the tables (definitions,
constraints, indexes) and replays the `TABLE DATA` via COPY, in
dependency order — parents before children — so the foreign keys are
satisfied.

Now the whole point of Step 1: prove nothing was lost by comparing the
restored counts against the baseline you saved.

```bash
PGPASSWORD=lab psql -h db -U postgres -d store -tA <<'SQL' > /tmp/counts_after
SELECT 'customers='   || count(*) FROM customers;
SELECT 'products='    || count(*) FROM products;
SELECT 'orders='      || count(*) FROM orders;
SELECT 'order_items=' || count(*) FROM order_items;
SQL

diff /tmp/counts_before /tmp/counts_after && echo "RESTORE VERIFIED: counts match"
```

If `diff` prints nothing and you see `RESTORE VERIFIED`, the restore is
byte-for-count identical to the original: `customers=50`,
`products=200`, `orders=500`, `order_items=1500`.

Notes for the real world:

- Restoring into a database that still has objects can raise "already
  exists" errors. `pg_restore --clean` drops objects first;
  `--if-exists` avoids errors when they don't. For a truly clean target,
  restore into a fresh `CREATE DATABASE`.
- `pg_restore -j N` parallelizes data + index rebuild for large dumps.
- A backup is only proven when a *restore* has succeeded — test your
  restores, not just your dumps.

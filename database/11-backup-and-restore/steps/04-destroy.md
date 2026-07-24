# Simulate the disaster

Backups only matter when something goes wrong. Let's make it go wrong —
on purpose.

You're going to drop all four tables. Because `orders` and `order_items`
have foreign keys pointing at `customers` and `products`, you can't drop
the parents while children still reference them — `CASCADE` drops the
dependent objects too.

```bash
PGPASSWORD=lab psql -h db -U postgres -d store
```

```sql
DROP TABLE IF EXISTS order_items CASCADE;
DROP TABLE IF EXISTS orders      CASCADE;
DROP TABLE IF EXISTS products    CASCADE;
DROP TABLE IF EXISTS customers   CASCADE;
```

Confirm the damage — the query should now error or return nothing:

```sql
\dt
SELECT count(*) FROM customers;   -- ERROR: relation "customers" does not exist
```

This is the moment that separates teams with a backup from teams without
one. In production this might have been an accidental
`DROP TABLE`, a bad migration, or a truncate-the-wrong-environment
mistake. Your data is gone from the live database — but you have
`/tmp/backup.dump`.

> In real life you would `DROP DATABASE` / `CREATE DATABASE` to restore
> into a truly clean database. Here we drop the tables so the same `store`
> database stays available for the restore in the final step.

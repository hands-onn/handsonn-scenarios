# Combine tables with JOIN

Two tables: `customers` and `orders`. Each order references a customer by
`customer_id`. A **JOIN** stitches them together on that relationship.

```bash
PGPASSWORD=lab psql -h db -U postgres -d shop
```

```sql
SELECT o.id, c.name, o.amount, o.status
FROM orders o
JOIN customers c ON c.id = o.customer_id;
```

An `INNER JOIN` (just `JOIN`) returns only rows that match on both sides.
Every order has a customer, so all 6 orders appear.

Write the row count to `/tmp/join_count`:

```bash
PGPASSWORD=lab psql -h db -U postgres -d shop -tAc \
  "SELECT count(*) FROM orders o JOIN customers c ON c.id=o.customer_id" > /tmp/join_count
cat /tmp/join_count
```

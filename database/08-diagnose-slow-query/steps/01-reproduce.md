# A join that shouldn't be this slow

You have two tables in the `shop` database:

- `orders` — 120,000 rows. Column `status` is **skewed**: ~95% are
  `shipped`, a sliver are `refunded`.
- `line_items` — ~360,000 rows. Each row points back to an order via
  `line_items.order_id`.

Product wants a report of the line items on every **refunded** order.
That's a tiny slice of orders, so the query *should* be fast. Connect and
try it:

```bash
PGPASSWORD=lab psql -h db -U postgres -d shop
```

```sql
SELECT o.id, o.status, li.sku, li.qty
  FROM orders o
  JOIN line_items li ON li.order_id = o.id
 WHERE o.status = 'refunded';
```

It works, but it feels sluggish for such a small result. Don't guess —
ask Postgres exactly what it did. `EXPLAIN (ANALYZE, BUFFERS)` runs the
query and reports the real plan: per-node timings, row counts, and how
many disk/cache pages (`BUFFERS`) each node touched.

Capture the full plan to a file:

```bash
PGPASSWORD=lab psql -h db -U postgres -d shop -c \
"EXPLAIN (ANALYZE, BUFFERS)
 SELECT o.id, o.status, li.sku, li.qty
   FROM orders o
   JOIN line_items li ON li.order_id = o.id
  WHERE o.status = 'refunded';" > /tmp/plan_before

cat /tmp/plan_before
```

Read the whole plan. In the next step you'll pin down what makes it slow.

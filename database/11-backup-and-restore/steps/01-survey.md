# Know what you're protecting

You never take a backup blind. Before you dump anything, record what
"good" looks like — so that after a restore you can prove nothing was
lost.

This `store` database has four related tables:

| table         | what it holds          |
|---------------|------------------------|
| `customers`   | who buys               |
| `products`    | what's for sale        |
| `orders`      | one per placed order   |
| `order_items` | line items per order   |

Open a psql session against the `db` pod (its service DNS is just `db`):

```bash
PGPASSWORD=lab psql -h db -U postgres -d store
```

List the tables and eyeball the counts:

```sql
\dt
SELECT count(*) FROM customers;
SELECT count(*) FROM products;
SELECT count(*) FROM orders;
SELECT count(*) FROM order_items;
```

Now capture those baseline counts to a file so you have something to
compare against later. Run this from the pod shell (not inside psql):

```bash
PGPASSWORD=lab psql -h db -U postgres -d store -tA <<'SQL' > /tmp/counts_before
SELECT 'customers='   || count(*) FROM customers;
SELECT 'products='    || count(*) FROM products;
SELECT 'orders='      || count(*) FROM orders;
SELECT 'order_items=' || count(*) FROM order_items;
SQL
cat /tmp/counts_before
```

You should see `customers=50`, `products=200`, `orders=500` and
`order_items=1500`.

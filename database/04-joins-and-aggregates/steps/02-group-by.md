# Aggregate with GROUP BY

`GROUP BY` collapses rows into groups so you can compute per-group
aggregates (`SUM`, `COUNT`, `AVG`, ...). Filter which rows enter the
aggregate with `WHERE`; filter the aggregated groups with `HAVING`.

Total **paid** revenue per customer, highest first:

```sql
SELECT c.name, SUM(o.amount) AS revenue
FROM orders o
JOIN customers c ON c.id = o.customer_id
WHERE o.status = 'paid'
GROUP BY c.name
ORDER BY revenue DESC;
```

Carol has one 200 order; Alice has 100+50=150; Bob 75. Write the top
spender's name:

```bash
PGPASSWORD=lab psql -h db -U postgres -d shop -tAc \
  "SELECT c.name FROM orders o JOIN customers c ON c.id=o.customer_id
   WHERE o.status='paid' GROUP BY c.name ORDER BY SUM(o.amount) DESC LIMIT 1" > /tmp/top_customer
cat /tmp/top_customer
```

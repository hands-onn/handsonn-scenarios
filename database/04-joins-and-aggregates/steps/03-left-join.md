# LEFT JOIN — include the unmatched

An `INNER JOIN` drops rows with no match. A `LEFT JOIN` keeps every row
from the left table, filling `NULL` where the right side has no match.
That's how you find "customers with no orders":

```sql
SELECT c.name, o.id AS order_id
FROM customers c
LEFT JOIN orders o ON o.customer_id = c.id
WHERE o.id IS NULL;
```

Dan has never ordered, so his row survives the LEFT JOIN with a NULL
order — and the `WHERE o.id IS NULL` isolates exactly those.

```bash
PGPASSWORD=lab psql -h db -U postgres -d shop -tAc \
  "SELECT count(*) FROM customers c LEFT JOIN orders o ON o.customer_id=c.id WHERE o.id IS NULL" > /tmp/no_orders
cat /tmp/no_orders
```

This "LEFT JOIN ... WHERE right IS NULL" is the canonical anti-join
pattern — find rows in A with no counterpart in B.

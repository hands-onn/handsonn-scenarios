# Sort and limit

`ORDER BY` sorts; `LIMIT` caps the row count. Together they answer
"top N" questions.

Most expensive product:

```sql
SELECT name, price
FROM products
ORDER BY price DESC
LIMIT 1;
```

Write just the name to `/tmp/answer2`:

```bash
PGPASSWORD=lab psql -h db -U postgres -d shop -tAc \
  "SELECT name FROM products ORDER BY price DESC LIMIT 1" > /tmp/answer2
cat /tmp/answer2
```

`ORDER BY price ASC` (the default) would give cheapest first. `LIMIT`
without `ORDER BY` returns arbitrary rows — always pair them.

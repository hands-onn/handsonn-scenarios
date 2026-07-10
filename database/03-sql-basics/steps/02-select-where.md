# Filter rows with WHERE

`WHERE` restricts which rows come back. Combine conditions with `AND`/`OR`.

Find hardware products that are actually in stock:

```sql
SELECT name, price, in_stock
FROM products
WHERE category = 'hardware' AND in_stock > 0;
```

How many rows? Count them directly:

```sql
SELECT count(*)
FROM products
WHERE category = 'hardware' AND in_stock > 0;
```

Write that number to `/tmp/answer` (run this from the shell, not inside psql):

```bash
PGPASSWORD=lab psql -h db -U postgres -d shop -tAc \
  "SELECT count(*) FROM products WHERE category='hardware' AND in_stock>0" > /tmp/answer
cat /tmp/answer
```

`-t` = tuples only (no header), `-A` = unaligned, `-c` = run one command.
Great for scripting query results.

# Step 3 — Fix the database view

Connect to the DB:

```bash
PGPASSWORD=lab psql -h db -U postgres -d orders
```

Inspect the view:

```sql
\d+ orders_view
```

Look at how `total` is computed — it divides by zero, so every query
against the view raises `division by zero` and the API returns 500.
Replace the view so `total` is computed correctly from
`quantity * unit_price`.

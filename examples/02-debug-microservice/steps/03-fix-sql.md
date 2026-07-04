# Step 3 — Fix the database view

Connect to the DB:

```bash
PGPASSWORD=lab psql -h db -U postgres -d orders
```

Inspect the view:

```sql
\d+ orders_view
```

The `total` column references a column that no longer exists. Replace the view so `total` is computed correctly from `quantity * unit_price`.

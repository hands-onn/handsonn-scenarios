# Connect to Postgres

A Postgres database named `shop` is running in a pod reachable at the
hostname `db`. Your terminal has `psql`. Connect:

```bash
PGPASSWORD=lab psql -h db -U postgres -d shop
```

You're now at the `shop=#` prompt. Useful meta-commands:

```
\dt              -- list tables
\d products      -- describe the products table
\q               -- quit
```

Take a look at the data:

```sql
SELECT * FROM products;
```

Seven rows. Click **Check** once you can query the table.

Drop the tables with `CASCADE` so the foreign keys don't block you:

```sql
DROP TABLE IF EXISTS order_items CASCADE;
DROP TABLE IF EXISTS orders      CASCADE;
DROP TABLE IF EXISTS products    CASCADE;
DROP TABLE IF EXISTS customers   CASCADE;
```

The verify counts matching rows in `information_schema.tables` for the
public schema and passes when it finds `0` of the four tables left.

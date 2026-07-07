Drop and re-create the view:

```sql
DROP VIEW orders_view;
CREATE VIEW orders_view AS
SELECT id, customer_id, quantity * unit_price AS total
FROM orders;
```

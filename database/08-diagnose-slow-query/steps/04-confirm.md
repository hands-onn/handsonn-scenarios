# The Seq Scan should be gone

Re-run the plan on the exact same query and see what changed:

```sql
EXPLAIN
SELECT o.id, o.status, li.sku, li.qty
  FROM orders o
  JOIN line_items li ON li.order_id = o.id
 WHERE o.status = 'refunded';
```

The `Seq Scan on line_items` should be gone. Because `refunded` is only a
small fraction of orders, the planner now typically picks a **Nested Loop**
that, for each refunded order, does an **Index Scan on line_items** using
`idx_line_items_order_id` — reading only the handful of matching rows.

```
Nested Loop
  ->  Seq Scan on orders o   (Filter: status = 'refunded')   <- fine, tiny result
  ->  Index Scan using idx_line_items_order_id on line_items li
        Index Cond: (order_id = o.id)
```

A Seq Scan on the small filtered `orders` set can still appear — that's
fine and often *correct*, because it returns few rows. What matters is that
`line_items` is no longer scanned end-to-end.

If you still see `Seq Scan on line_items`:

- Did you run `ANALYZE line_items` after creating the index? The planner
  needs fresh statistics.
- Is the index really on `order_id`? Check with `\di line_items*`.

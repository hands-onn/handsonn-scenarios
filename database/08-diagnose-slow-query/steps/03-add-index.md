# Index the join column

The fix is to give Postgres a fast lookup on the column it joins on:
`line_items.order_id`. With an index, for each matching `refunded` order
the planner can jump straight to that order's line items (an index-driven
nested loop) instead of scanning all 360k rows.

```bash
PGPASSWORD=lab psql -h db -U postgres -d shop
```

```sql
CREATE INDEX idx_line_items_order_id ON line_items (order_id);
```

Then refresh the planner's statistics so it knows the index exists and how
selective it is:

```sql
ANALYZE line_items;
```

Confirm it's there:

```sql
\di line_items*
```

Why index `order_id` and not, say, `orders.status`? The plan told us: the
runaway cost was the full scan of `line_items`. Index the column that the
expensive node needed — the **join key** — not just any column in the
query. (Indexing `orders.status` could also help the planner find the
`refunded` slice, but the dominant cost was the un-indexed join side.)

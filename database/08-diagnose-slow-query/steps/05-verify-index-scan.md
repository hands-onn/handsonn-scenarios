# Prove the win with numbers

You've confirmed the Seq Scan is gone. Now capture the improved plan *with
real timings* and see the payoff. Run `EXPLAIN (ANALYZE, BUFFERS)` again and
save it, so you can compare before/after:

```bash
PGPASSWORD=lab psql -h db -U postgres -d shop -c \
"EXPLAIN (ANALYZE, BUFFERS)
 SELECT o.id, o.status, li.sku, li.qty
   FROM orders o
   JOIN line_items li ON li.order_id = o.id
  WHERE o.status = 'refunded';" > /tmp/plan_after

cat /tmp/plan_after
```

Compare the two plans side by side:

```bash
echo '=== BEFORE ==='; cat /tmp/plan_before
echo '=== AFTER  ==='; cat /tmp/plan_after
```

What to look for in the AFTER plan:

- `line_items` is now reached via an **Index Scan** (or **Bitmap Index
  Scan**) on `idx_line_items_order_id`, not a Seq Scan.
- The `Buffers:` counts on the line-items node dropped sharply — it reads a
  few index/heap pages instead of the whole table.
- Total `Execution Time` is far lower than before.

That's the full loop: measure with `EXPLAIN (ANALYZE, BUFFERS)`, find the
dominant node, index the column it needed, re-measure to prove it worked.

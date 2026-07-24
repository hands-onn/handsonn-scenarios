# Read the plan bottom-up

EXPLAIN output is a tree. **Leaf nodes run first**; read from the most
indented lines upward. In `/tmp/plan_before` you'll see something like:

```
Hash Join  (cost=... rows=... width=...) (actual time=... rows=... loops=1)
  Hash Cond: (li.order_id = o.id)
  ->  Seq Scan on line_items li  (cost=... rows=360000 ...) (actual time=... rows=360000 ...)
  ->  Hash  (...)
        ->  Seq Scan on orders o  (cost=... rows=... ) (actual ... rows≈... )
              Filter: (status = 'refunded'::text)
              Rows Removed by Filter: ~118800
```

Two things to notice:

1. **`Seq Scan on line_items`** reads all ~360,000 rows even though the
   final result is tiny. That node's `actual time` and `Buffers:` dominate
   the plan — it's the bottleneck. It happens because `line_items.order_id`
   has **no index**, so to join, Postgres must read every line item.

2. **Estimate vs. actual on `orders`**: `status` is skewed, so the planner
   only expects a handful of `refunded` rows (`rows=...` small). Compare it
   to `actual rows`. A large gap between estimated and actual rows is how
   you spot a *bad estimate* that pushed the planner toward a full scan.

The dominant cost is the sequential scan of the un-indexed join column.
Record your diagnosis (name the table):

```bash
echo "Bottleneck: Seq Scan on line_items (order_id un-indexed) dominates the join." > /tmp/diagnosis
cat /tmp/diagnosis
```

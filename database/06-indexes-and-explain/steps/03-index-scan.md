# See the plan change

Run the exact same query again:

```sql
EXPLAIN ANALYZE
SELECT * FROM events WHERE user_id = 42;
```

The top node should now read **`Index Scan using idx_events_user_id`**
(or a Bitmap Index Scan) instead of `Seq Scan`. Execution time drops
sharply — Postgres walks the B-tree to the matching rows instead of
reading all 100k.

Capture the new plan:

```bash
PGPASSWORD=lab psql -h db -U postgres -d shop -tAc \
  "EXPLAIN SELECT * FROM events WHERE user_id = 42" | head -1 > /tmp/plan_after
cat /tmp/plan_after
```

If you still see a Seq Scan: did you run `ANALYZE events`? The planner
relies on fresh statistics to choose the index. Also, on tiny tables
Postgres may *correctly* prefer a seq scan — that's why we loaded 100k
rows.

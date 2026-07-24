# You diagnosed a slow query from its plan — not from guesswork.

The repeatable workflow for any slow query:

1. **Measure.** `EXPLAIN (ANALYZE, BUFFERS)` runs the query and reports the
   real plan: per-node `actual time`, row counts, and pages touched.
2. **Read bottom-up.** Leaf nodes execute first. Find the node with the
   largest `actual time` / `Buffers` — that's your bottleneck.
3. **Spot the classic culprits:**
   - a **Seq Scan** on a big table feeding a join or a selective filter,
   - an un-indexed **join key** (what bit us here),
   - a big gap between **estimated and actual rows** — a bad estimate,
     often from skewed data or stale statistics.
4. **Fix the specific node.** Index the column the expensive node needed —
   here, the join key `line_items(order_id)`. Then `ANALYZE` so the planner
   has fresh statistics.
5. **Re-measure.** Confirm the plan changed (Seq Scan -> Index Scan) and
   that `Buffers` and `Execution Time` actually dropped.

Key ideas:

- Index the column the **plan** blames, not just any column in the query.
  The plan tells you where the cost is.
- **Skew matters.** When most rows share one value (`status='shipped'`), a
  filter for the rare value (`refunded`) is highly selective — an index and
  a nested loop beat a full scan. The planner needs current statistics
  (`ANALYZE`) to see that.
- The planner is **cost-based**: a Seq Scan on a small or mostly-returned
  table is often the right choice. Don't chase every Seq Scan — only the
  one dominating the plan.
- `BUFFERS` exposes I/O the timings alone hide: shared hits vs. reads tell
  you whether the data was cached or pulled from disk.

You now have a method, not a hunch, for taming slow queries.

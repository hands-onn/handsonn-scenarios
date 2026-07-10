# You can now reason about query performance.

The workflow for any slow query:

1. **`EXPLAIN ANALYZE`** the query. Read from the bottom up (leaf nodes
   run first).
2. Spot the expensive node: a **Seq Scan** on a big table with a
   selective filter is the classic culprit; so are **nested-loop joins**
   over large inputs.
3. **Index** the filtered/joined column(s). B-tree by default; there are
   also GIN (arrays/JSONB/full-text), GiST, BRIN (huge append-only) etc.
4. **`ANALYZE`** so the planner has current statistics.
5. Re-check the plan — confirm it flipped to an Index Scan and the time
   dropped.

Key ideas:
- Indexes speed reads, slow writes, cost space. Index deliberately.
- Composite indexes `(a, b)` serve filters on `a` and `a,b` — order
  matters (leftmost-prefix rule).
- The planner is cost-based: it may ignore an index if a query returns
  most of the table (a seq scan is genuinely cheaper then).

Next: **locks-and-debugging** — diagnose a query blocked on a lock.

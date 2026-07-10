# SQL fundamentals down.

The query skeleton you'll reach for constantly:

```sql
SELECT columns
FROM   table
WHERE  conditions      -- filter rows
ORDER BY column [DESC] -- sort
LIMIT  n;              -- cap results
```

And the scripting form — `psql -tAc "..."` returns a bare value, ideal
for shell pipelines and health checks.

Next: **joins-and-aggregates** — combine tables and compute GROUP BY
summaries.

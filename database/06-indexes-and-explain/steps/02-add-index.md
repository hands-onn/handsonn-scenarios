# Add an index

An index is a sorted lookup structure (a B-tree by default) that lets
Postgres jump straight to matching rows instead of scanning everything.

```sql
CREATE INDEX idx_events_user_id ON events (user_id);
```

Then update the planner's statistics so it knows the index exists and how
selective it is:

```sql
ANALYZE events;
```

Verify the index is there:

```sql
\di events*
```

Indexes aren't free — they cost disk space and slow down writes (every
INSERT/UPDATE must maintain them). Index the columns you actually filter
or join on, not everything.

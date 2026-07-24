# Where did the slots go?

Connect as the superuser (you still have reserved slots) and ask the live
connection view, `pg_stat_activity`, to break the backends down by state:

```bash
PGPASSWORD=lab psql -h db -U postgres -d shop -c \
  "SELECT count(*), state FROM pg_stat_activity GROUP BY state"
```

You'll see a large pile of **`idle`** connections. `idle` means
*connected, holding a slot, but running no query* — the connection is
doing nothing except occupying a seat. A healthy app with a busy
workload has a few `active` and a handful of `idle`; seeing the pool
maxed out with `idle` is the signature of a **connection leak** (an app
or pooler that opens connections and never closes them).

Look closer — who owns them, and how long have they sat idle?

```bash
PGPASSWORD=lab psql -h db -U postgres -d shop -c "
  SELECT pid, usename, state,
         now() - state_change AS idle_for,
         left(query, 40) AS last_query
  FROM pg_stat_activity
  WHERE usename = 'app'
  ORDER BY state_change;"
```

Every one of them is `state = idle`, owned by `app`, idle since it
connected. That is your culprit.

Record what you found — the state that is hogging the pool:

```bash
echo "idle" > /tmp/diagnosis
```

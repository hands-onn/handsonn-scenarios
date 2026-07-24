# Confirm the app can connect again

The slots are free. Do exactly what the app does — open a brand-new
connection as the ordinary `app` role. The connection that failed in
step 1 should now succeed:

```bash
PGPASSWORD=lab psql -h db -U app -d shop -tAc 'SELECT 1' | tee /tmp/reconnect
```

You should see `1` — a clean login on the `app` role, no `FATAL`. Service
restored.

Sanity-check the pool one more time:

```bash
PGPASSWORD=lab psql -h db -U postgres -d shop -c \
  "SELECT count(*), state FROM pg_stat_activity GROUP BY state"
```

Plenty of headroom now. You reproduced the outage, located the idle
leak, reclaimed the slots, and verified recovery — the full incident
loop. The debrief covers how to stop it recurring.

# The app can't connect

The on-call page reads: *"orders-service: FATAL: too many clients."* The
database is up, queries you run by hand work — yet the application can't
get in. Something has eaten all the connection slots.

This Postgres was started with a deliberately small budget:

```bash
PGPASSWORD=lab psql -h db -U postgres -d shop -c "SHOW max_connections"
PGPASSWORD=lab psql -h db -U postgres -d shop -c "SHOW superuser_reserved_connections"
```

`max_connections = 20`, and `superuser_reserved_connections = 3` are held
back for superusers. So an ordinary role has **17** slots — and the app
role, `app`, is not a superuser.

Reproduce the failure the app sees. Connect as `app`:

```bash
PGPASSWORD=lab psql -h db -U app -d shop -c 'SELECT 1' 2>&1 | tee /tmp/exhaustion
```

You'll get a `FATAL` — either `sorry, too many clients already` or
`remaining connection slots are reserved for non-replication superuser
connections`. Either way: **no slot for the app**. That output is now
saved in `/tmp/exhaustion`.

Notice you (as the `postgres` superuser) can still connect — that's what
the 3 reserved slots are for. This is your lifeline for diagnosing a
saturated database. Leave the slots full and move to the next step.

# Connection exhaustion, end to end.

The incident loop you just ran:

1. **Reproduce** — the app got `too many clients` / `slots reserved`
   while you (superuser) still got in. Those reserved slots
   (`superuser_reserved_connections`) exist precisely so an operator can
   diagnose a saturated database.
2. **Diagnose** — `SELECT count(*), state FROM pg_stat_activity GROUP BY
   state` showed the pool maxed out with **`idle`** connections: a leak,
   not real load.
3. **Reclaim** — `pg_terminate_backend(pid)` freed the leaked slots.
   (`pg_cancel_backend(pid)` only cancels a running query; it won't help
   an *idle* session — there's nothing to cancel.)
4. **Recover** — a fresh `app` connection succeeded.

## Why this happens

Each Postgres connection is a full OS process with its own memory. They
are expensive, and `max_connections` is a hard cap. Apps that open a
connection per request (or per thread) and forget to close them pile up
as `idle` sessions and eventually lock everyone out — even though the
database is barely doing any work.

## Stopping it for good

- **Put a pooler in front of Postgres.** **PgBouncer** in
  `pool_mode = transaction` lets hundreds or thousands of client
  connections share a small pool of real server connections — the app
  gets a connection instantly, PgBouncer hands it a real backend only for
  the duration of a transaction. This is the standard fix for
  "too many clients." (PgBouncer / pgcat / the RDS Proxy all do this.)
- **Size the app pool deliberately.** The sum of every service's max pool
  size must stay under `max_connections`. A common mistake is 10 replicas
  x a 20-connection pool = 200 connections against a `max_connections`
  of 100.
- **Kill idle leaks automatically.** Set
  `idle_in_transaction_session_timeout` (idle mid-transaction) and, in
  the pooler, an idle-client timeout so leaked sessions get reaped.
- **Monitor `pg_stat_activity`.** Alert on total connections approaching
  `max_connections`, and on rising `idle` / `idle in transaction` counts.

Raising `max_connections` feels like the fix but rarely is — every extra
slot costs memory and the leak just refills them. Pool, size, and time
out instead.

That extends the Postgres set: queries -> joins -> schema -> indexes ->
locks -> connections.

# Lock debugging, end to end.

The playbook when "a query is hanging":

1. **Confirm it's a lock**, not slowness: set `SET lock_timeout` and see
   if it errors with a lock timeout.
2. **`pg_stat_activity`** — the live connection list. `state`,
   `wait_event_type`, and the `query` text tell the story.
3. **`pg_blocking_pids(pid)`** — exactly which backends block a waiter.
4. **Resolve**: `pg_cancel_backend(pid)` (cancel query) or
   `pg_terminate_backend(pid)` (kill connection, release locks).

The root cause here — **idle in transaction** — is one of the most
common production lock issues: an app runs `BEGIN`, does work, then
fails to `COMMIT`/`ROLLBACK` (bug, crash, or a connection pool holding
the session). Defenses:
- Set `idle_in_transaction_session_timeout` so Postgres auto-kills them.
- Keep transactions short; never hold one open across a network call or
  user think-time.
- Watch `pg_stat_activity` for `idle in transaction` in monitoring.

That completes the Postgres set: queries → joins → schema → indexes →
locks.

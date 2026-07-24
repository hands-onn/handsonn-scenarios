# Deadlocks, untangled.

A deadlock is not slowness and not a stuck lock — it's a **cycle** in the
waits-for graph: two (or more) transactions each holding a lock the other
needs. Postgres won't hang on it; the deadlock detector wakes after
`deadlock_timeout` (default 1s), finds the cycle, and **aborts a victim**
with `ERROR: deadlock detected`.

The playbook you just ran:

1. **Reproduce deterministically.** Deadlocks are timing bugs. Force the
   interleaving (here: each session takes its first lock, then a barrier
   makes it wait for the peer before grabbing the second) so it happens
   every time instead of "sometimes in prod".
2. **Capture the error and the victim.** The victim's session log carries
   `ERROR: deadlock detected`; the survivor commits. Which one is victim
   can vary — read, don't assume.
3. **Read the cycle.** The log `DETAIL` names the processes and the
   `ShareLock on transaction` each waits for. Live, `pg_blocking_pids()`
   over `pg_stat_activity` shows each backend blocked by the other.
4. **Fix the lock order.** The root cause was **inconsistent ordering** —
   one path locked 1 then 2, the other 2 then 1. Impose a single
   canonical order (lowest id first, or any total order every transaction
   agrees on) and a cycle can no longer form. The worst case degrades to
   a short *wait*, not a deadlock.

## What to remember

- Ordering, not luck. Always acquire multiple locks in the same global
  order across your whole codebase (`ORDER BY id` + `FOR UPDATE`, sort
  keys before a multi-row update, etc.).
- Keep transactions short and grab all the rows you need up front — the
  longer two transactions overlap, the wider the window for a cycle.
- Deadlocks *will* still happen occasionally under load. Make the app
  **retry** on SQLSTATE `40P01` (deadlock_detected): the victim's work
  was rolled back cleanly, so a retry is safe and usually succeeds.
- Contrast with the *idle-in-transaction* block from the locks lab: that
  was one session holding a lock forever (fix: terminate it / set
  `idle_in_transaction_session_timeout`). A deadlock is mutual and
  self-resolving — you fix the *code's* lock order, not a single session.

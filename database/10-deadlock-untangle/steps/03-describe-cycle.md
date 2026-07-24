# Read the cycle

A deadlock is a **cycle in the waits-for graph**: A waits for B, and B
waits for A. Postgres can show you who blocks whom while it's happening
through `pg_blocking_pids()` and `pg_locks`, and it prints the cycle in
the log `DETAIL` after the fact.

Two ways to see it:

**1. The log DETAIL you already have.** The victim's log names the
processes and the lock:

```bash
grep -A2 -i 'deadlock detected' /tmp/sess_a.log /tmp/sess_b.log
```

The `DETAIL` line reads like "Process X waits for ShareLock on
transaction T2; blocked by process Y" and "Process Y waits for ... ;
blocked by process X" — that mutual wait *is* the cycle.

**2. Watch it live (optional).** `pg_blocking_pids()` returns, for a
given backend, the array of PIDs blocking it. During a two-session lock
wait you'd see each backend listing the other:

```bash
PGPASSWORD=lab psql -h db -U postgres -d bank -c "
  SELECT pid, pg_blocking_pids(pid) AS blocked_by,
         state, left(query,40) AS query
  FROM pg_stat_activity
  WHERE datname='bank' AND pid <> pg_backend_pid();"
```

## Write down what happened

In your own words, explain the cycle. Your notes should mention:

- both rows involved — account **1** and account **2**
- that each session locked one row and then **waited** for the other
- that they took locks in **opposite / reverse order**, forming a cycle

```bash
cat > /tmp/diagnosis.txt <<'EOF'
sess_a locked account 1 (FOR UPDATE) then waited for account 2.
sess_b locked account 2 then waited for account 1.
Opposite lock order => a wait cycle 1->2->1 => deadlock.
EOF
```

The check verifies your note mentions rows 1 and 2, the reversed order,
and the waiting/locking.

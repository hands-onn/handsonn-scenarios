# A transfer that eats itself

You run a tiny bank. Table `accounts` has two rows:

| id | owner | balance |
|----|-------|---------|
| 1  | alice | 1000    |
| 2  | bob   | 1000    |

A money transfer must lock **both** the source and destination rows
(`SELECT ... FOR UPDATE`) before moving the balance, so no one else can
touch them mid-flight. The buggy code locks them in **the order the
caller asked for** — source first, then destination.

Now two transfers fire at the same time in opposite directions:

- **sess_a**: move 100 from account **1 -> 2** (locks 1, then 2)
- **sess_b**: move 100 from account **2 -> 1** (locks 2, then 1)

sess_a grabs row 1 and waits for row 2. sess_b grabs row 2 and waits for
row 1. Neither can proceed. That is a **deadlock**.

## Reproduce it deterministically

You only have one terminal, so we script both sessions as background
`psql` jobs. Each one takes its first lock, drops a marker, waits for the
other's marker, then reaches for the second lock — guaranteeing the cycle
forms every run. A helper wires them together:

```bash
sh /tmp/run_pair.sh /tmp/transfer.sh "1 2 100" "2 1 100"
```

Postgres' deadlock detector wakes up after `deadlock_timeout` (1s here),
notices the cycle, and **aborts one transaction** to break it. Look:

```bash
grep -i 'deadlock' /tmp/sess_a.log /tmp/sess_b.log
```

Exactly one log will show:

```
ERROR:  deadlock detected
DETAIL:  Process ... waits for ShareLock on transaction ...; blocked by process ...
```

The check re-runs the pair and confirms a deadlock was detected.

# SIGKILL — the unignorable one

If the process is still alive after SIGTERM, escalate with SIGKILL (9).
The kernel kills it directly; the process cannot trap or ignore it.

```bash
kill -9 $(cat /tmp/pid)
```

Or by name (careful — matches every process with that name):

```bash
pkill -9 runaway-worker
```

Confirm it's gone:

```bash
ps -p $(cat /tmp/pid) || echo "no such process"
```

`pkill` (kills by name) and `pgrep` (finds by name) are handy but
dangerous — always `pgrep` first to see what you'd hit.

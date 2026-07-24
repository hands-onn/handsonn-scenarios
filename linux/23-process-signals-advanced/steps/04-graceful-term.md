# Shut it down gracefully: SIGTERM (15)

**SIGTERM** is the polite "please shut down" signal — and it's what `kill`
sends when you give it no signal name. Unlike SIGKILL, a process can **trap**
it to flush buffers, close connections, remove its pidfile, and exit on its
own terms. That's *graceful shutdown*, and it's why orchestrators
(Kubernetes, systemd, Docker) send SIGTERM first and only escalate to SIGKILL
after a grace period.

This daemon's TERM handler:
- writes a cleanup sentinel to `/tmp/daemon.cleanup`,
- removes its own pidfile `/tmp/daemon.pid`,
- then `exit 0`.

Send it:

```bash
kill $(cat /tmp/found_pid)          # SIGTERM is the default
# same thing, explicit:
kill -TERM $(cat /tmp/found_pid)
kill -15   $(cat /tmp/found_pid)
```

Give it a moment (its idle loop wakes at most once a second), then verify a
**clean** exit — cleanup ran *and* the process is no longer alive:

```bash
cat /tmp/daemon.cleanup                 # "graceful shutdown ... flushing state"
test -e /tmp/daemon.pid && echo "pidfile still there" || echo "pidfile removed"
ps -o pid,stat,comm | grep -w "$(cat /tmp/found_pid)" || echo "no such process"
```

If the sentinel exists and the pidfile is gone, you've shut it down the right
way.

> **A note on this pod:** PID 1 here is `sleep infinity`, not a real init, so
> it never *reaps* its children. After the daemon calls `exit`, it may linger
> briefly as a **zombie** — `ps` shows state `Z` (procps also labels it
> `<defunct>`), and `/proc/<pid>` still exists. That's expected: a zombie is a *dead* process
> whose exit status hasn't been collected. It holds no CPU or memory and is
> not "still running." A proper init (systemd, tini, or Kubernetes' pause
> container) would reap it instantly.

> Escalation ladder for a stuck process: **SIGTERM (15)** → wait a few
> seconds → **SIGKILL (9)**. Reach for `kill -9` only when a process ignores
> or hangs on SIGTERM — it gives no chance to clean up.

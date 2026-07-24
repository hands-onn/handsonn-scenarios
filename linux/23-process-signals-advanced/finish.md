# Signals mastered.

You drove a live daemon through its whole signal lifecycle — the same moves
you'd make on a real service in production.

## What each signal did

| Signal | # | Trappable? | You used it to |
|---|---|---|---|
| `SIGHUP`  | 1  | yes | Reload config without restarting (marker + counter) |
| `SIGTERM` | 15 | yes | Graceful shutdown: run cleanup, then exit 0 |
| `SIGSTOP` | 19 | **no** | Freeze the process → state `T` |
| `SIGCONT` | 18 | (resumes) | Thaw it → back to `S`/`R` |
| `SIGKILL` | 9  | **no** | Last resort: kernel kills it, no cleanup |
| `SIGINT`  | 2  | yes | What Ctrl-C sends |

**SIGKILL (9) and SIGSTOP (19) are the only two a process can never catch,
block, or ignore.** Everything else can be trapped — which is exactly how
daemons implement reload and graceful shutdown.

## Traps, in shell

```sh
on_hup()  { reload_config; }
on_term() { flush_state; rm -f "$PIDFILE"; exit 0; }
trap on_hup  HUP
trap on_term TERM
while true; do sleep 1; done   # signal interrupts sleep, handler runs, loop resumes
```

## Reading process state (the STAT / `/proc/<pid>/stat` field 3)

- `R` running · `S` interruptible sleep · `D` uninterruptible sleep (blocked on I/O)
- `T` stopped (SIGSTOP / job-control) · `t` traced · `Z` zombie (dead, un-reaped)

## The production playbook

1. **Reload, don't restart** — `kill -HUP` (or `systemctl reload`) applies
   config changes with zero downtime when the app supports it.
2. **Terminate gracefully first** — `kill` / `kill -TERM`, and give the
   process its grace period to clean up.
3. **Escalate only if it hangs** — `kill -9`. No cleanup, no pidfile removal,
   possible corruption — that's the cost of SIGKILL.
4. **Pause, don't kill, to inspect** — `kill -STOP` freezes a process so you
   can attach a debugger or take a snapshot; `kill -CONT` resumes it.
5. **Signal by PID, confirm by /proc** — `pgrep -af`, `/proc/<pid>/cmdline`,
   and `/proc/<pid>/stat` keep you from paging the wrong process.

# Process triaged.

Signals worth knowing:

| Signal | Number | What it means |
|---|---|---|
| `SIGTERM` | 15 | Please shut down (catchable) |
| `SIGKILL` | 9 | Die now (uncatchable, use last) |
| `SIGHUP` | 1 | Reload config (many daemons) |
| `SIGINT` | 2 | Ctrl-C |
| `SIGSTOP`/`SIGCONT` | 19/18 | Pause / resume |

Real diagnosis order for a runaway:
1. `top` / `htop` → note PID.
2. `ls -l /proc/PID/exe` → what binary is it?
3. `cat /proc/PID/cmdline` → what args?
4. `cat /proc/PID/status` → threads, memory, state.
5. `strace -p PID` → what syscalls?
6. Then decide: `kill`, `kill -9`, or a bug report.

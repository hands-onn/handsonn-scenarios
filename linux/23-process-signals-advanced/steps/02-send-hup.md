# Reload with SIGHUP (1)

**SIGHUP** was historically "the terminal hung up," but by convention daemons
repurpose it to mean **"re-read your config without restarting."** nginx,
sshd, rsyslog, HAProxy — they all reload on SIGHUP so you don't drop live
connections just to change a setting.

This daemon traps SIGHUP: each one appends a line to `/tmp/daemon.reload` and
bumps a reload counter at `/tmp/daemon.reloads`.

Send it a HUP by PID:

```bash
kill -HUP $(cat /tmp/found_pid)
# equivalently, by number:
kill -1 $(cat /tmp/found_pid)
```

Now confirm the trap actually fired — the daemon should still be **alive**
(a reload is not a restart):

```bash
cat /tmp/daemon.reloads          # should be >= 1
cat /tmp/daemon.reload           # one "reloaded config ..." line per HUP
ps -o pid,stat,comm | grep -w "$(cat /tmp/found_pid)"   # still there
```

Send a couple more HUPs and watch the counter climb — that's the proof the
process caught the signal and ran *your* handler instead of dying. A process
with **no** SIGHUP handler would simply be terminated by it.

# Evict the squatter

You've confirmed the owner of 8080 is a stray `http.server`. Nothing else
depends on it, so reclaim the port by stopping it.

Ask it to shut down cleanly first (SIGTERM):

```bash
kill $(cat /tmp/port_pid)
```

Then confirm both that the process is gone **and** that the socket is free:

```bash
# Is the process still around?
test -d /proc/$(cat /tmp/port_pid) && echo "still alive" || echo "gone"

# Is anything still listening on 8080? (no output = free)
ss -tln 'sport = :8080'
```

If it ignored SIGTERM and is still holding the port, escalate to the
unignorable SIGKILL:

```bash
kill -9 $(cat /tmp/port_pid)
```

A freed port shows **no** LISTEN row for `:8080`. Note that a socket can
briefly linger in `TIME_WAIT` after the owner dies — but a *listening*
socket (what `-l` shows) disappears immediately once the process exits.

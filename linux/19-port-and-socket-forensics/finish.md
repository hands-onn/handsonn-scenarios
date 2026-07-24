# Port reclaimed.

You ran the standard "who owns this port?" playbook:

1. **Symptom** — `bind()` failed with `EADDRINUSE` (errno 98). The port had
   an owner.
2. **Find the owner** — `ss -tlnp` mapped the socket to a PID.
3. **Confirm identity** — `/proc/<pid>/cmdline` (NUL-separated!) and
   `/proc/<pid>/exe` told you exactly what it was, so you didn't kill the
   wrong thing.
4. **Evict** — `kill` (SIGTERM), escalating to `kill -9` (SIGKILL) only if
   needed.
5. **Reclaim** — your own listener bound cleanly.

## The commands, distilled

```bash
ss -tlnp                       # every listening TCP socket + owner
ss -tlnp 'sport = :8080'       # just port 8080
ss -tulpn                      # add UDP (-u) too
lsof -i :8080                  # same answer, if lsof is installed
fuser -k 8080/tcp              # find AND kill the owner in one shot (blunt)
```

## Reading `ss` output

```
State   Recv-Q Send-Q Local Address:Port  Peer Address:Port  Process
LISTEN  0      5      0.0.0.0:8080        0.0.0.0:*          users:(("python3",pid=42,fd=3))
```

- `0.0.0.0:8080` = listening on **all** interfaces. `127.0.0.1:8080` would
  be loopback-only.
- `users:((...pid=42...))` = the owning process. No `-p`, no PID (and you
  often need root to see PIDs you don't own).

## Gotchas worth remembering

- **`TIME_WAIT`**: after a connection closes, the socket lingers ~60s. A new
  *listener* isn't blocked by this, but re-connecting from the same
  client port can be. `SO_REUSEADDR` is why servers can restart instantly.
- **Wildcard vs specific bind**: a process on `0.0.0.0:8080` conflicts with
  one trying `127.0.0.1:8080`. Check the *address*, not just the port.
- **Kill by PID, not by name**: `pkill -9 python3` is a foot-gun. You had
  the exact PID from `ss` — use it.

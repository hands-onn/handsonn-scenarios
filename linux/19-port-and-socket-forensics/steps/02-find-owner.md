# Who owns the socket?

`ss` (socket statistics — the modern replacement for `netstat`) can list
every listening TCP socket *and* the process behind it.

```bash
ss -tlnp
```

Flag by flag:

| Flag | Meaning |
|---|---|
| `-t` | TCP sockets |
| `-l` | only **l**istening sockets |
| `-n` | **n**umeric ports (don't resolve `8080` → `http-alt`) |
| `-p` | show the owning **p**rocess |

Find the row for `*:8080` (or `0.0.0.0:8080`). The `-p` column looks like:

```
LISTEN 0 5 0.0.0.0:8080 0.0.0.0:* users:(("python3",pid=42,fd=3))
```

You can also filter straight to the port:

```bash
ss -tlnp 'sport = :8080'
```

Read the **pid=** value and save it — every later step keys off it:

```bash
echo <PID> > /tmp/port_pid
```

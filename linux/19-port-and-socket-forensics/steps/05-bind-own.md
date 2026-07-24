# Claim the port

The port is yours now. Start your own listener on 8080 — this time it
should bind without complaint.

Run it detached so it keeps running after this step (just like a real
service), and send its output to a log:

```bash
nohup python3 -m http.server 8080 >/tmp/mine.log 2>&1 &
```

Give it a second, then confirm *your* process now owns the socket:

```bash
ss -tlnp 'sport = :8080'
```

The `pid=` in that row should be a **new** PID — different from the one you
saved in `/tmp/port_pid`. Prove it end-to-end with a request:

```bash
curl -s http://127.0.0.1:8080/ | head
```

That's the full forensic loop: **symptom (EADDRINUSE) -> owner (`ss -tlnp`)
-> identity (`/proc`) -> eviction (`kill`) -> reclaim.**

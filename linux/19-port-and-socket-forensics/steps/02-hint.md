The PID lives inside the `users:(("name",pid=NNN,fd=N))` field. To pull just the number:

```bash
ss -tlnp 'sport = :8080' | grep -oE 'pid=[0-9]+' | grep -oE '[0-9]+' > /tmp/port_pid
cat /tmp/port_pid
```

If `-p` shows nothing, you'd normally need root — but here the squatter runs as your own user, so its PID is visible.

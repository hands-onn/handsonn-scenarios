If your new listener also fails with EADDRINUSE, the squatter isn't actually dead — go back and `kill -9 $(cat /tmp/port_pid)`, verify with `ss -tln 'sport = :8080'` (must be empty), then retry.

The `&` backgrounds the job; `nohup` and redirecting to `/tmp/mine.log` stop it from being tied to your terminal. Check it started with `jobs` and `ss -tlnp 'sport = :8080'`.

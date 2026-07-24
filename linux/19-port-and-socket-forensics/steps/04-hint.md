`kill` with no signal sends SIGTERM (15). If `ss -tln 'sport = :8080'` still shows a LISTEN row a second later, the process didn't exit — use `kill -9 $(cat /tmp/port_pid)`.

Avoid `pkill -9 python3` here: it would kill *every* python3, not just the squatter. You already have the exact PID, so target it directly.

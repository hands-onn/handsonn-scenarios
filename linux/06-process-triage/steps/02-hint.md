`kill 1234` sends SIGTERM to PID 1234. Check whether it's still around with `ps -p 1234` or `test -d /proc/1234 && echo alive || echo gone`.

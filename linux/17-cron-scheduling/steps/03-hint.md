`crond -b -l 8` starts the busybox cron daemon in the background. `pgrep crond` should then find it. If "command not found", it's at /usr/sbin/crond.

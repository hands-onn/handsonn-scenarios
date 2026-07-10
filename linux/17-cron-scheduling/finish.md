# Scheduling, understood.

The pieces:

1. **crontab** — the schedule table (5 time fields + command). Install
   with `crontab -`, list with `crontab -l`, edit with `crontab -e`.
2. **cron daemon** — actually fires the jobs. Must be running
   (`systemctl start cron` on real hosts).
3. **Timing** — jobs run at field boundaries; `* * * * *` = every minute.

Real-world cron gotchas:
- **Minimal environment**: cron runs with a bare `PATH` and no shell
  profile. Use absolute paths (`/usr/bin/foo`) and set vars explicitly.
- **No output = silent failure**: cron mails output nowhere useful in
  containers. Always redirect: `>> /var/log/job.log 2>&1`.
- **Overlapping runs**: if a job runs longer than its interval, you get
  concurrent copies. Guard with `flock`.
- Modern alternative: **systemd timers** (`OnCalendar=`) give logging via
  journald, dependencies, and randomized delays.

(The Kubernetes equivalent — CronJobs — is covered in the K8s track.)

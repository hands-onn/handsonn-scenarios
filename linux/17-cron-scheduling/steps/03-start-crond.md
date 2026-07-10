# Start the cron daemon

Installing a crontab doesn't run anything — the **cron daemon** must be
running to fire jobs. On a normal system systemd runs it; here you start
it directly:

```bash
crond -b -l 8
```

- `-b` background (daemonize)
- `-l 8` log level 8 (verbose, so you can see it fire)

Confirm it's running:

```bash
pgrep -a crond
```

On systemd hosts you'd instead use `systemctl start cron` (Debian) or
`systemctl start crond` (RHEL), and `journalctl -u cron` to see its logs.

# Force a rotation now

Normally `logrotate` runs on a timer and only rotates when its
conditions (age/size) are met. You don't want to wait — force it:

```bash
logrotate -f /etc/logrotate.d/app
```

- `-f` / `--force` runs the rotation even if the size/time trigger hasn't
  fired yet, which is exactly what you want for a first test.
- Add `-v` (verbose) to watch it decide and act:
  `logrotate -fv /etc/logrotate.d/app`.
- `-d` (debug) does a dry run and prints what it *would* do without
  touching anything — handy for validating a policy safely.

Now look at the directory:

```bash
ls -lh /var/log/app/
```

You should see a new archive alongside the live log:

```
app.log        <- live log (now much smaller)
app.log.1      <- the rotated copy  (or app.log.1.gz once compressed)
```

The check passes once `app.log.1` **or** `app.log.1.gz` exists.

> Note: `logrotate` records rotation timestamps in a state file
> (`--state <file>`, default `/var/lib/logrotate/logrotate.status`). If it
> ever "refuses" to rotate on a re-run, that state file thinks it already
> rotated recently — `-f` overrides it. If it complains about state-file
> permissions, point it somewhere writable:
> `logrotate -f -s /tmp/logrotate.status /etc/logrotate.d/app`.

# Write the rotation policy

`logrotate` reads per-app policy files from `/etc/logrotate.d/`. Create
one for our log at `/etc/logrotate.d/app`. The policy names the log path,
then lists directives inside `{ ... }`.

You need four behaviors:

- **`size 1M`** — rotate once the file passes this size (rather than on a
  fixed daily schedule). You can also use `100k`, `500M`, etc.
- **`rotate 5`** — keep 5 old generations, then discard the oldest. This
  is the knob that actually caps disk usage.
- **`compress`** — gzip rotated copies (`app.log.1.gz`) to save space.
- **`copytruncate`** — **copy** the log to the archive, then **truncate**
  the original in place. This is essential here: our writer holds the log
  file open and never reopens it. A default (move-based) rotation would
  rename `app.log` out from under the writer, which would keep writing to
  the now-unlinked inode — the fresh `app.log` would stay empty. With
  `copytruncate`, the file descriptor stays valid and logging continues.

Write the file. `logrotate` config isn't a shell script, so a here-doc is
the clean way to lay it down exactly:

```bash
cat > /etc/logrotate.d/app <<'EOF'
/var/log/app/app.log {
    size 1M
    rotate 5
    compress
    copytruncate
    missingok
    notifempty
}
EOF
```

`missingok` (skip quietly if the log is absent) and `notifempty` (don't
rotate an empty file) are good hygiene but optional for this check.

Verify what you wrote:

```bash
cat /etc/logrotate.d/app
```

The check confirms the file targets `/var/log/app/app.log` and contains
`size`, `rotate N`, `compress`, and `copytruncate`.

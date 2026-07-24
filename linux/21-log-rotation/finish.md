# Log rotation, tamed.

You put a real rotation policy in place and proved it works end to end:

1. **Policy** in `/etc/logrotate.d/app` — `size` (when), `rotate N` (how
   many to keep = the disk cap), `compress` (shrink archives),
   `copytruncate` (rotate safely for apps that hold the fd open).
2. **Forced run** with `logrotate -f` produced `app.log.1[.gz]`.
3. **In-place truncate** — the live log shrank while the archive kept the
   history.
4. **Uninterrupted logging** — the writer kept appending to the same
   file, no restart required.

## The two rotation strategies

| Strategy | How it works | When to use |
|---|---|---|
| **create** (default) | rename `log` → `log.1`, create fresh `log` | App reopens its log on `SIGHUP`/inotify (nginx, most daemons). Cheapest — no copy. |
| **copytruncate** | copy `log` → `log.1`, then truncate `log` in place | App holds the fd open and can't reopen. Small race: lines written between copy and truncate can be lost. |

## Directives worth remembering

- **`daily` / `weekly` / `monthly` / `size`** — the trigger. Combine with
  `maxsize`/`minsize` for hybrids.
- **`rotate N`** — retention count; `maxage N` discards by age instead.
- **`compress` + `delaycompress`** — gzip archives, but keep the most
  recent one uncompressed (nice for `tail`).
- **`dateext`** — name archives `app.log-20260724` instead of `.1`, `.2`.
- **`postrotate … endscript`** — run a hook after rotating (e.g.
  `kill -HUP` / `systemctl reload`) so the app reopens its log.
- **`missingok` / `notifempty`** — don't error on a missing log, don't
  rotate an empty one.

## In production

`logrotate` is normally driven by `/etc/logrotate.conf` (which `include`s
`/etc/logrotate.d/`) on a **daily cron job or systemd timer** — you rarely
call it by hand except to test with `-d` (debug/dry-run) or `-f` (force).
For containerized apps, prefer logging to **stdout/stderr** and letting the
container runtime or a log shipper handle rotation and retention — but when
an app insists on writing files, this is the tool.

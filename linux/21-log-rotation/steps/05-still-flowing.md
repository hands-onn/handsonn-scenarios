# Prove logging never skipped a beat

The real test of a good rotation policy: **did the app keep logging?**
Because `copytruncate` preserved the writer's open file descriptor, the
background writer should still be appending to the same `app.log` — the
one you just watched shrink.

Watch it grow in real time:

```bash
tail -f /var/log/app/app.log
```

Every couple of seconds a new `heartbeat` line lands. Press Ctrl-C when
you've seen a few. Compare the size before and after a short wait:

```bash
stat -c %s /var/log/app/app.log; sleep 6; stat -c %s /var/log/app/app.log
```

The second number must be larger — new writes are flowing into the live
file, not into a lost/unlinked inode. The check does exactly this: it
samples the size, waits, and requires it to have grown.

Contrast that with what would have happened *without* `copytruncate`: a
move-based rotation renames `app.log` to `app.log.1` and creates a brand
new `app.log`. But the writer still holds the old inode open, so it keeps
writing into the renamed file — and the new `app.log` stays empty until
the app is restarted or told to reopen its logs (e.g. via `SIGHUP` or
`logrotate`'s `postrotate` hook). `copytruncate` sidesteps that entirely
for apps that can't reopen on demand.

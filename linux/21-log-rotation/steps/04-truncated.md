# Confirm the live log was truncated in place

Rotation only helps if the *live* file actually shrank. Compare sizes:

```bash
ls -lh /var/log/app/
stat -c %s /var/log/app/app.log        # live: should be small now
stat -c %s /var/log/app/app.log.1 2>/dev/null || \
  stat -c %s /var/log/app/app.log.1.gz # archive: holds the old bulk
```

What you should see:

- `app.log.1` (or `app.log.1.gz`) is large — it captured the ~2 MiB of
  history that was there before.
- `app.log` is tiny — only the handful of lines written since the
  truncate, growing a little every couple of seconds.

That size flip is the signature of `copytruncate`: logrotate **copied**
the accumulated content into the archive, then **truncated** the original
file to zero length *without deleting or renaming it*. The inode — and
therefore the writer's open file descriptor — is untouched.

Confirm the inode really is the same file the writer still holds:

```bash
ls -li /var/log/app/app.log            # note the inode number
stat -c '%i %s' /var/log/app/app.log
```

The check verifies the live log is now far smaller than the archive
(and under 512 KiB in absolute terms). If the live log is still huge,
`copytruncate` didn't take effect — revisit the policy and re-run
`logrotate -f`.

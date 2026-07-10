# Write and install a crontab

You'll schedule a job that appends a timestamped line every minute.
Install it via `crontab` (which stores it where the daemon looks):

```bash
echo '* * * * * date >> /tmp/heartbeat' | crontab -
```

- `crontab -` reads the crontab from stdin and installs it.
- The command `date >> /tmp/heartbeat` appends the current time each run.

Verify it's installed:

```bash
crontab -l
```

You should see your single line. `crontab -e` opens an editor for
interactive editing; `crontab -r` removes it entirely.

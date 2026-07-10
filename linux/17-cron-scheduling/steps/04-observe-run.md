# Wait for it to fire

cron runs jobs at the **top of the minute**. Your job appends to
`/tmp/heartbeat` — watch it appear (this can take up to ~60s for the
next minute boundary):

```bash
# watch the file grow
while true; do echo "--- $(date +%H:%M:%S) ---"; cat /tmp/heartbeat 2>/dev/null; sleep 5; done
```

Press Ctrl-C once you see a line. Each minute adds another.

If nothing appears after 90s:
- Is the daemon running? `pgrep crond`
- Is the entry installed? `crontab -l`
- Check the daemon's own logging (`crond -f -l 8` in the foreground
  prints each job as it runs).

The click **Check** once `/tmp/heartbeat` has content.

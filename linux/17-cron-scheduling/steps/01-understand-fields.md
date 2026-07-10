# The five cron fields

A crontab line is five time fields + a command:

```
┌───────── minute        (0-59)
│ ┌─────── hour          (0-23)
│ │ ┌───── day of month  (1-31)
│ │ │ ┌─── month         (1-12)
│ │ │ │ ┌─ day of week   (0-6, Sun=0)
│ │ │ │ │
* * * * *  command-to-run
```

`*` means "every". So `* * * * *` = **every minute**. Some more:

| Schedule | Meaning |
|---|---|
| `*/5 * * * *` | every 5 minutes |
| `0 * * * *` | top of every hour |
| `0 2 * * *` | 02:00 daily |
| `0 9 * * 1` | 09:00 every Monday |

Write what `* * * * *` means:

```bash
echo "runs every minute" > /tmp/cron_meaning
cat /tmp/cron_meaning
```

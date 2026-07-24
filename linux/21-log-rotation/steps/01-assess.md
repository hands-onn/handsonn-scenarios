# A log that never stops growing

An app on this box writes to `/var/log/app/app.log` and **never rotates
it**. The process holds the file open and appends forever, so the file
just grows until the disk fills. Your job: put a rotation policy in place
so the log gets archived and trimmed automatically.

First, make sure the tool we need is even here:

```bash
command -v logrotate
```

You should get a path like `/usr/sbin/logrotate`. `logrotate` is the
standard utility for age/size-based log rotation on Linux; on real hosts
it's usually driven daily by cron or a systemd timer, but you can also
invoke it by hand.

Now size up the problem. See how big the log already is:

```bash
ls -lh /var/log/app/app.log
stat -c %s /var/log/app/app.log      # size in bytes
tail -n 3 /var/log/app/app.log       # it's actively being written
```

It's already a couple of megabytes and climbing. Record the current byte
count so we know you looked:

```bash
stat -c %s /var/log/app/app.log > /tmp/logsize
```

The recorded number should be over a million (bytes). Next you'll write
the policy that tames it.

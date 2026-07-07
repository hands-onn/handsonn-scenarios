# Fix the listen port

`nginx -t` now passes, but starting will still fail. Try it:

```bash
nginx 2>&1 | head
```

You'll see something like:

```
bind() to 0.0.0.0:80 failed (13: Permission denied)
```

This pod runs without `CAP_NET_BIND_SERVICE`, so any port under 1024 is
forbidden. Change the `listen 80` to `listen 8080`:

```bash
sed -i 's/listen 80/listen 8080/' /etc/nginx/sites-enabled/default
```

Re-check:

```bash
grep listen /etc/nginx/sites-enabled/default
nginx -t
```

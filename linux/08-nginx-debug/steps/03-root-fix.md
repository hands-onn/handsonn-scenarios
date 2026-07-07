# Fix the document root

nginx now starts, but all requests come back 404. Check the error log
to see why:

```bash
tail /var/log/nginx/error.log
```

You'll see entries like `open() "/var/www/nope/index.html" failed
(2: No such file or directory)`. The `root` directive points at
`/var/www/nope`, which doesn't exist. There *is* content at
`/var/www/html/index.html`.

Change the root:

```bash
sed -i 's|root /var/www/nope;|root /var/www/html;|' /etc/nginx/sites-enabled/default
```

Reload the daemon so it picks up the change:

```bash
nginx -s reload
```

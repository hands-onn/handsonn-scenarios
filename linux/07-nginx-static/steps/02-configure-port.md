# Change the listen port to 8080

The default nginx site listens on `80`, but this pod runs with the
`CAP_NET_BIND_SERVICE` capability dropped — binding to ports below 1024
will fail. We'll use `8080` instead.

The file to edit is `/etc/nginx/sites-enabled/default`. Look for the
`listen 80 default_server;` and `listen [::]:80 default_server;` lines
and change `80` to `8080`.

Fastest way with sed:

```bash
sed -i 's/listen 80/listen 8080/g' /etc/nginx/sites-enabled/default
sed -i 's/listen \[::\]:80/listen [::]:8080/g' /etc/nginx/sites-enabled/default
```

Or open it with `vi` / `nano` and edit by hand.

Verify:

```bash
grep listen /etc/nginx/sites-enabled/default
```

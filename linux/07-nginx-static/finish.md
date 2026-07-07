# Nginx is up.

The path you just walked:

1. `apt-get install -y nginx` — package + service files land in `/etc/nginx`.
2. Config lives in `/etc/nginx/nginx.conf` (main) and
   `/etc/nginx/sites-enabled/*` (per-site includes).
3. `nginx -t` before every reload — a syntax error stops the whole server.
4. `nginx -s reload` for zero-downtime config changes;
   `nginx -s stop` to shut down.

Next: **nginx-debug** hands you a broken config and asks you to fix it
using `nginx -t` and the error log.

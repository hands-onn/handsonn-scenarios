# Install nginx

This pod is a minimal Debian image — no web server yet. Install nginx
via apt:

```bash
apt-get install -y nginx
```

The install takes ~15 seconds. When it's done, check where nginx landed:

```bash
which nginx
nginx -v
```

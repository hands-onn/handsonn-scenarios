# Configure nginx to use the cert

Rewrite the default site to listen on `8443` with TLS. Overwriting the
whole file is fine here:

```bash
cat > /etc/nginx/sites-enabled/default <<'EOF'
server {
  listen 8443 ssl;
  server_name _;
  ssl_certificate     /etc/nginx/certs/server.crt;
  ssl_certificate_key /etc/nginx/certs/server.key;

  # Modest defaults for a lab. Real deployments should pin
  # protocols/ciphers per current Mozilla guidance.
  ssl_protocols TLSv1.2 TLSv1.3;

  root /var/www/html;
  index index.html;

  location / {
    try_files $uri $uri/ =404;
  }
}
EOF
```

Put an index page in place so we have something to serve:

```bash
echo "<h1>TLS works</h1>" > /var/www/html/index.html
```

Validate and reload:

```bash
nginx -t && nginx || nginx -s reload
```

# Drop an index.html

nginx's default document root is `/var/www/html`. Whatever's at
`/var/www/html/index.html` is what visitors see.

Overwrite the default page:

```bash
cat > /var/www/html/index.html <<'EOF'
<!doctype html>
<html>
<head><title>Handsonn</title></head>
<body>
<h1>Hello from handsonn</h1>
</body>
</html>
EOF
```

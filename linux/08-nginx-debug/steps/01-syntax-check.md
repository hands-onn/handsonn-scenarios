# Find the syntax error

nginx won't start. Ask it to validate the config:

```bash
nginx -t
```

It'll point at a file and a line number. Open the offending file:

```bash
cat -n /etc/nginx/sites-enabled/default
```

The message will say something like `unexpected end of file, expecting "}"`.
That's nginx telling you a block never closed. Add the missing `}` at
the correct level, save, and re-run `nginx -t` until it says
**"syntax is ok"**.

Editor of choice:

```bash
vi /etc/nginx/sites-enabled/default
# or, if you prefer:
apt-get install -y nano && nano /etc/nginx/sites-enabled/default
```

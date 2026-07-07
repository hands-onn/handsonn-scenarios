# Start nginx and verify

First check the config syntactically:

```bash
nginx -t
```

If it says "syntax is ok", start the daemon:

```bash
nginx
```

(No systemd in this pod — you run `nginx` directly. It daemonizes by
default.)

Now curl your site:

```bash
curl -v http://127.0.0.1:8080
```

You should see `Hello from handsonn` in the body. If the config needs
adjusting later, reload with:

```bash
nginx -s reload
```

`nginx -s reload` re-reads config without dropping connections — the
proper way to apply changes in production.

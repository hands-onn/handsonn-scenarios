# Fix it properly

There are three levers you can pull. Two are legitimate, one is a
smoke bomb.

### Legitimate: connect using the correct hostname

The cert is for `wrong-name.example`. If you can convince curl that
that name resolves to `127.0.0.1` for this one request, the hostname
check will pass:

```bash
curl --resolve wrong-name.example:8443:127.0.0.1 \
     --cacert /etc/nginx/certs/server.crt \
     -v https://wrong-name.example:8443/
```

- `--resolve HOST:PORT:IP` — override DNS for this request only.
  Equivalent to a temporary `/etc/hosts` entry.
- `--cacert PATH` — trust this specific CA cert. Way better than `-k`.

That's the correct fix when the cert is right and your DNS is wrong.

### Legitimate: fix the cert

If the cert is wrong (typo, expired, missing SAN), reissue it. In
production that means going back to your CA / cert-manager /
certbot / Let's Encrypt.

### Smoke bomb: -k / --insecure

```bash
curl -k https://127.0.0.1:8443/    # DON'T
```

This turns off verification entirely. Whoever answers at
`127.0.0.1:8443` — MITM, wrong service, anyone — you'll trust them.
Fine for a one-shot poke; **never** in scripts, cron jobs, or checked-in
code. Use `--cacert` and `--resolve` instead.

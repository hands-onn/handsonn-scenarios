# Verify the handshake

Trying `curl` without `-k` (or `--insecure`) will fail — the cert is
self-signed, no CA trusts it:

```bash
curl -v https://127.0.0.1:8443
```

You'll see `SSL certificate problem: self-signed certificate`. That's
expected. Add `-k` to skip verification:

```bash
curl -kv https://127.0.0.1:8443
```

Look for the `SSL connection using TLSv1.3` line and the `HTTP/1.1 200
OK` — you've got TLS termination working.

For a closer look at the handshake itself:

```bash
openssl s_client -connect 127.0.0.1:8443 -servername handsonn </dev/null 2>&1 | head -30
```

Useful lines:
- `subject=CN = handsonn` — the CN you set.
- `Verify return code: 18 (self-signed certificate)` — proves the cert
  chain is being checked (and, as expected, doesn't validate).
- `Protocol: TLSv1.3` — negotiated protocol.

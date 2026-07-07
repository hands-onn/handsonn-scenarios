# Generate a self-signed certificate

`openssl req` can create a key + certificate in one shot. You'll be
prompted for identity fields — the Common Name is what matters here,
set it to `handsonn`.

Non-interactive one-liner:

```bash
openssl req -x509 -newkey rsa:2048 -nodes \
  -keyout /etc/nginx/certs/server.key \
  -out /etc/nginx/certs/server.crt \
  -days 30 \
  -subj "/CN=handsonn"
```

Breakdown:
- `-x509` — output a self-signed cert (skip the CSR-then-sign dance).
- `-newkey rsa:2048` — generate a fresh 2048-bit RSA key.
- `-nodes` — don't encrypt the private key (nginx won't be able to
  read it if you do).
- `-days 30` — validity window.
- `-subj "/CN=handsonn"` — skip the interactive prompts.

Inspect the result:

```bash
openssl x509 -in /etc/nginx/certs/server.crt -noout -subject -dates
```

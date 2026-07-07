# TLS terminated.

You've done the whole loop: generate a keypair + self-signed cert,
point nginx at them, verify with `curl` and `openssl s_client`.

For real deployments, replace the self-signed cert with one from
**Let's Encrypt** (via `certbot`) or your internal CA. Nothing else in
the nginx config needs to change — just the cert and key files.

Related scenarios:
- **openssl-certs** — inspect certificates you didn't generate.
- **tls-debug** — track down "unable to verify certificate" errors.

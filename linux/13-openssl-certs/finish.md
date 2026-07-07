# You just ran a mini-PKI.

The three files, and who holds what:

| File | Held by | Purpose |
|---|---|---|
| `ca.key` | The CA (private!) | Signs certs. Compromise = catastrophe. |
| `ca.crt` | Everyone | Trust anchor. Distributed to clients. |
| `server.key` | The server (private!) | Proves ownership of `server.crt`. |
| `server.csr` | Ephemeral | Applicant → CA. Discardable after signing. |
| `server.crt` | The server (public) | Presented on TLS handshake. |

The same shape scales up to Let's Encrypt (their `ca.crt` is in every
OS trust store; ACME automates the CSR → sign dance) and enterprise
PKIs (internal CA in HSMs; requests go through Vault or a private CA
appliance).

Cheat sheet — the openssl subcommands you'll actually use:

```bash
# Inspect a cert
openssl x509 -in cert.pem -text -noout
openssl x509 -in cert.pem -noout -subject -issuer -dates -fingerprint

# Inspect a CSR
openssl req -in req.csr -text -noout

# Verify a chain
openssl verify -CAfile ca.crt cert.pem

# Convert PEM ↔ DER
openssl x509 -in cert.pem -outform DER -out cert.der
openssl x509 -in cert.der -inform DER -out cert.pem

# Extract a public key from a cert
openssl x509 -in cert.pem -noout -pubkey
```

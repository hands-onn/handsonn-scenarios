# Make a root CA

A "Certificate Authority" is just a keypair whose public half signs
other certs. We'll build a tiny one.

Generate the CA key + a self-signed CA cert:

```bash
# Key
openssl genrsa -out /pki/ca.key 2048

# Self-signed CA cert (10 years is fine for a lab CA)
openssl req -x509 -new -nodes \
  -key /pki/ca.key \
  -sha256 -days 3650 \
  -out /pki/ca.crt \
  -subj "/CN=Handsonn Lab Root CA/CN=Handsonn"
```

Inspect what you just created:

```bash
openssl x509 -in /pki/ca.crt -text -noout | head -30
```

The important fields:
- **Subject** and **Issuer** are the same (root CA is self-signed).
- **Basic Constraints** should include `CA:TRUE` — this is what marks
  it as a signing authority. If a cert says `CA:FALSE`, it can't sign
  other certs even if you try.

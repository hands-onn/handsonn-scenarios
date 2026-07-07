# Certificate Signing Request (CSR)

A CSR is what a would-be certificate holder sends to a CA. It contains:

- The applicant's **public key**.
- The **subject** they want on the cert.
- A signature proving they hold the matching private key.

Generate a server key + CSR for `api.handsonn.lab`:

```bash
# Server key
openssl genrsa -out /pki/server.key 2048

# CSR
openssl req -new \
  -key /pki/server.key \
  -out /pki/server.csr \
  -subj "/CN=api.handsonn.lab"
```

Read it back to confirm:

```bash
openssl req -in /pki/server.csr -noout -subject -verify
```

`-verify` re-checks the applicant's self-signature on the CSR.

The private key **never leaves** the applicant's machine. Only the CSR
goes to the CA. That's the whole point of asymmetric crypto here.

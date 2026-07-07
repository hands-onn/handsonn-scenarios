# Sign the CSR

The CA takes the CSR, decides "yes, this applicant is who they say they
are", and signs it — producing a real certificate.

```bash
openssl x509 -req \
  -in /pki/server.csr \
  -CA /pki/ca.crt \
  -CAkey /pki/ca.key \
  -CAcreateserial \
  -out /pki/server.crt \
  -days 90 \
  -sha256
```

Verify the chain:

```bash
openssl verify -CAfile /pki/ca.crt /pki/server.crt
```

Expected output:

```
/pki/server.crt: OK
```

Inspect the signed cert:

```bash
openssl x509 -in /pki/server.crt -noout -issuer -subject -dates
```

You should see `issuer=CN=Handsonn Lab Root CA/CN=Handsonn` and
`subject=CN=api.handsonn.lab`. That's the mathematical proof that our
CA vouches for this server.

### Making it usable

Anyone who trusts `/pki/ca.crt` will now trust `/pki/server.crt`. In
real deployments:

- Corporate: install `ca.crt` in the OS trust store (`/etc/ssl/certs/`
  + `update-ca-certificates`).
- Web browsers: import it into the browser's cert store.
- Kubernetes: bundle the CA into every ConfigMap/Secret consumed by
  workloads that call the API.

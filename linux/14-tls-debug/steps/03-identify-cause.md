# So — why does it fail?

You know two facts now:

1. You connected to `127.0.0.1`.
2. The cert's Common Name is `wrong-name.example`.

TLS verification requires **both** trust-chain checks and a
**hostname match**. Even if the CA were trusted, requesting
`https://127.0.0.1/` and receiving a cert for `wrong-name.example`
is a mismatch.

Two failures are stacking here:

- **Untrusted CA** — cert is self-signed, no root store trusts it.
- **Hostname mismatch** — even if the CA were trusted, the name
  doesn't line up.

Write your diagnosis:

```bash
echo "hostname mismatch: cert CN is wrong-name.example but we connected to 127.0.0.1" > /tmp/diagnosis
```

Real-world triage checklist when you see "unable to verify certificate":

1. What CN / SANs does the cert have? (`openssl s_client ... | x509`).
2. What hostname did *your* client use in the URL?
3. Do they match? (Wildcard SANs match one label: `*.foo.com` matches
   `api.foo.com` but not `foo.com` or `api.b.foo.com`.)
4. Is the CA trusted by the system? (`ls /etc/ssl/certs/`.)
5. Is the cert expired? (dates in `x509 -dates`.)
6. Is the clock right? (`date`.)

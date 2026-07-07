# Inspect the cert directly

`openssl s_client` is a raw TLS client — no HTTP, no verification
short-circuits, just the handshake. Use it to see what the server is
actually presenting:

```bash
openssl s_client -connect 127.0.0.1:8443 -servername 127.0.0.1 </dev/null 2>/dev/null | \
  openssl x509 -noout -subject -issuer -dates
```

You'll see the cert's real Subject/Issuer/dates. Note the `-servername`
flag — that's the SNI (Server Name Indication) hostname. Servers with
multiple certs pick one based on SNI, so getting this right matters.

Save just the CN so the checker can confirm what you found:

```bash
openssl s_client -connect 127.0.0.1:8443 -servername 127.0.0.1 </dev/null 2>/dev/null | \
  openssl x509 -noout -subject | sed 's/.*CN\s*=\s*//' > /tmp/cn
cat /tmp/cn
```

Other s_client tricks:
- `-showcerts` — dump the entire chain the server sent.
- `-tls1_2` / `-tls1_3` — force a specific protocol.
- `-CAfile PATH` — validate against a specific CA.
- `-verify_return_error` — exit non-zero if verification fails
  (default is to warn and continue).

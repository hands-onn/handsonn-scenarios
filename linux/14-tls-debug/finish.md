# TLS debugging playbook

When a client says "unable to verify certificate", answer these five
questions in order:

1. **What cert did the server actually send?**
   `openssl s_client -connect HOST:PORT -servername HOST </dev/null | openssl x509 -noout -subject -issuer -dates -ext subjectAltName`

2. **Does the cert's Subject/SAN cover the hostname you used?**
   Wildcards match one label. IP addresses need an IP-SAN, not just a CN.

3. **Is the cert expired?**
   Look at `notBefore` / `notAfter`. Also check the local clock (`date`).

4. **Is the CA trusted?**
   `openssl verify -CAfile CA.crt cert.pem` — try against candidate
   CA bundles. On distros: `/etc/ssl/certs/ca-certificates.crt` or
   `/etc/pki/tls/certs/ca-bundle.crt`.

5. **Does the client see the CA?**
   Applications built with old bundles, or containers with a stripped
   trust store, may not see recent Let's Encrypt roots (e.g. the 2021
   ISRG cross-sign expiry issue).

Nine times out of ten it's #2. The remaining time it's #4, and usually
a container built too long ago.

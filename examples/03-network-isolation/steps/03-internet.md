# Step 3 — The public internet still works

Egress to the internet (anything outside RFC1918 and link-local) is allowed, so you can fetch packages, query public APIs, etc.

```bash
curl -fsS https://example.com/ | head -3
```

# Step 1 — Reach the API

Three containers are running side by side:

| pod | role |
|---|---|
| `shell` | your terminal |
| `api`   | the broken service (port 8080) |
| `db`    | postgres (port 5432) |

They share a namespace, so each pod is reachable by its name as a DNS hostname.

```bash
curl -i http://api:8080/healthz
```

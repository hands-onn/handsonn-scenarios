# Step 2 — Reproduce the failing endpoint

The product team says `/orders` returns `500`. Confirm it.

```bash
curl -i http://api:8080/orders
```

Look at the API logs for the SQL error:

```bash
kubectl logs api -n $LAB_NAMESPACE | tail
```

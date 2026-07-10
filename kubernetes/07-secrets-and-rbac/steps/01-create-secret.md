# Create a Secret

Secrets hold sensitive data (API keys, passwords, tokens) — like a
ConfigMap but with tighter handling. Create one:

```bash
kubectl create secret generic app-secret \
  --from-literal=API_KEY=super-secret-value
```

Inspect it:

```bash
kubectl get secret app-secret -o yaml
```

Note the value is **base64-encoded**, not encrypted — anyone who can
`get` the Secret can decode it:

```bash
kubectl get secret app-secret -o jsonpath='{.data.API_KEY}' | base64 -d; echo
```

That's exactly why RBAC (later steps) matters: base64 is not security,
so you control *who can read Secrets* instead.

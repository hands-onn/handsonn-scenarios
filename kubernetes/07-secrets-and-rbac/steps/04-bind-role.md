# Bind the Role to an identity

A Role does nothing until bound to a subject (user, group, or
ServiceAccount). Create a ServiceAccount and bind the Role to it:

```bash
kubectl create serviceaccount appsa

kubectl create rolebinding read-secrets \
  --role=secret-reader \
  --serviceaccount=default:appsa
```

Now test the grant with `kubectl auth can-i --as` — impersonation lets
you check permissions without deploying anything:

```bash
# allowed — the Role grants this
kubectl auth can-i get secrets --as=system:serviceaccount:default:appsa

# denied — the Role does NOT grant delete
kubectl auth can-i delete secrets --as=system:serviceaccount:default:appsa

# denied — not granted on pods at all
kubectl auth can-i get pods --as=system:serviceaccount:default:appsa
```

The first says `yes`, the others `no`. That's least-privilege RBAC:
`appsa` can read Secrets and do nothing else.

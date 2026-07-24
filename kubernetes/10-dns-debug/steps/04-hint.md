Change the Service selector from `app=order` to `app=orders`:

```bash
kubectl patch svc orders --type=merge -p '{"spec":{"selector":{"app":"orders"}}}'
```

Then `kubectl get endpoints orders` should list pod IPs instead of `<none>`. No pod changes are needed — the pods were always correct.

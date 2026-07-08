# Give it a stable address

Pods are ephemeral; their IPs change. A **Service** provides a stable
virtual IP + DNS name and load-balances across the matching pods.

```bash
kubectl expose deployment web --port=80
```

Check that the Service found its pods — the `Endpoints` list should show
the pod IPs:

```bash
kubectl get svc web
kubectl get endpoints web
kubectl describe svc web | grep -i endpoints
```

If Endpoints is empty, the Service's selector doesn't match any pod
labels — the #1 "my service returns nothing" cause.

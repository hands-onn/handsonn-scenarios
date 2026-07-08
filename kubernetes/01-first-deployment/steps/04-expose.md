# Expose the Deployment

Pods get new IPs every restart, so you never talk to them directly. A
**Service** gives the set of Pods one stable virtual IP + DNS name and
load-balances across them.

Create a ClusterIP Service for `web` on port 80:

```bash
kubectl expose deployment web --port=80
```

Inspect it:

```bash
kubectl get svc web
kubectl describe svc web
```

Note the `Endpoints` line — it lists the Pod IPs the Service routes to.
That list is maintained automatically as Pods come and go.

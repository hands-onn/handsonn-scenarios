# Watch the rollout

`kubectl rollout status` blocks until a Deployment's rollout completes —
essential in CI/CD pipelines to know when a deploy is actually live.

```bash
kubectl rollout status deployment/web
```

It should report `deployment "web" successfully rolled out`. Confirm all
3 replicas are ready:

```bash
kubectl get deploy web
```

The `READY` column should read `3/3`.

# What's running

A Deployment `web` is already running nginx:1.25 with 3 replicas. Look:

```bash
kubectl get deploy web
kubectl rollout history deployment/web
```

`rollout history` shows the revision list. Right now there's one
revision. Every spec change adds another — that history is what makes
rollbacks possible.

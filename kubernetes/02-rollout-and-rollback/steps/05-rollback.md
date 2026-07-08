# Roll back

You don't need to remember the old image tag — Kubernetes kept the
previous revision. Undo the last rollout:

```bash
kubectl rollout undo deployment/web
```

Confirm recovery:

```bash
kubectl rollout status deployment/web
kubectl get deploy web -o wide
```

`web` is back on nginx:1.27, all 3 replicas ready. In a real incident,
`kubectl rollout undo` is your fastest path back to a known-good state —
faster than finding and re-applying the old manifest.

# The naked Deployment

`web` is running, but it ships none of the production essentials — no
health probes, no resource requests/limits. Look:

```bash
kubectl get deploy web
kubectl get deploy web -o yaml | grep -A20 "containers:"
```

Notice: no `readinessProbe`, no `livenessProbe`, no `resources`. This
"works" in a demo and causes incidents in production:
- Without a **readiness probe**, Kubernetes sends traffic the instant the
  container process starts — before the app can actually serve.
- Without a **liveness probe**, a hung-but-not-crashed process is never
  restarted.
- Without **resource requests**, the scheduler can't place pods sensibly.
- Without **limits**, one pod can starve its neighbors.

When you've seen the gaps:

```bash
touch /tmp/baseline
```

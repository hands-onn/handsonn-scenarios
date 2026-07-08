# Requests and limits

- **requests** — what the scheduler reserves. Determines which node a pod
  lands on. Too low → pods pack onto a node and thrash; too high → wasted
  capacity.
- **limits** — the hard ceiling. Exceed the memory limit → `OOMKilled`.
  Exceed the CPU limit → throttled (not killed).

Set both on `web`:

```bash
kubectl patch deployment web --type=json -p='[
  {"op":"add","path":"/spec/template/spec/containers/0/resources","value":{
    "requests":{"cpu":"50m","memory":"64Mi"},
    "limits":{"cpu":"200m","memory":"128Mi"}
  }}
]'
```

Confirm and watch the rollout:

```bash
kubectl rollout status deployment/web
kubectl get deploy web -o jsonpath='{.spec.template.spec.containers[0].resources}{"\n"}'
```

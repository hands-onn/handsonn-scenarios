# Something's wrong with `api`

A Deployment named `api` was just created, but it's not healthy. Look:

```bash
kubectl get pods
```

You'll see the `api-*` pod with a high `RESTARTS` count and status
`CrashLoopBackOff`. That means: the container starts, exits, Kubernetes
restarts it, it exits again — and Kubernetes backs off (waits longer
between each restart) to avoid hammering.

Save the pod's name for later:

```bash
kubectl get pods -l app=api -o name | head -1 | cut -d/ -f2 > /tmp/podname
cat /tmp/podname
```

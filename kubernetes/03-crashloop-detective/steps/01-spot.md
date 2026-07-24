# Something's wrong with `api`

A Deployment named `api` was just created, but it's not healthy. Look:

```bash
kubectl get pods
```

You'll see the `api-*` pod with a high `RESTARTS` count and status
`CrashLoopBackOff`. That means: the container starts, exits, Kubernetes
restarts it, it exits again — and Kubernetes backs off (waits longer
between each restart) to avoid hammering.

You don't need to memorize that random `api-xxxxx` suffix — select the
pod by its label instead:

```bash
kubectl get pods -l app=api
```

Stash the name so this scenario can check your progress:

```bash
kubectl get pods -l app=api -o name | cut -d/ -f2 > /tmp/podname
cat /tmp/podname
```

# A namespace with no guardrails

Team A has its own namespace, `team-a`, and a running `shop` Deployment.
There is nothing stopping this team from consuming the whole cluster —
no cap on how much CPU/memory they can request, no floor or ceiling on
individual containers.

Look at what's there:

```bash
kubectl get all -n team-a
```

Now check for the two governance objects we're about to add. Both should
come back empty:

```bash
kubectl get resourcequota -n team-a
kubectl get limitrange -n team-a
```

Peek at the `shop` container's resources — notice it declares **none**:

```bash
kubectl -n team-a get deploy shop \
  -o jsonpath='{.spec.template.spec.containers[0].resources}{"\n"}'
```

You'll get an empty `{}`. A container with no requests lands in the
`BestEffort` QoS class — first to be evicted under memory pressure, and
invisible to the scheduler's capacity math. That's the problem we're
fixing.

Record what you found so this scenario can check your progress:

```bash
echo "team-a has no ResourceQuota and no LimitRange; shop containers request nothing (ungoverned)" > /tmp/before
```

# You wired up an HPA — and learned what it depends on.

What you built and why each piece matters:

1. **CPU request on the container.** CPU-utilization autoscaling is a
   percentage of the *request*. No request → no denominator → the HPA
   can't compute utilization. This is the single most common reason a
   freshly-created HPA shows `<unknown>`.
2. **The HPA object** — `scaleTargetRef` (what to scale),
   `minReplicas`/`maxReplicas` (the bounds), and a **target metric**
   (what to steer toward). `min` keeps a floor for availability; `max`
   is your safety ceiling on cost and blast radius.
3. **Reading status.** `kubectl get hpa` TARGETS and
   `kubectl describe hpa` Conditions/Events tell you whether scaling is
   *active*. `ScalingActive: False` / `FailedGetResourceMetric` means the
   object is fine but the metrics pipeline is missing.

## The metrics dependency (important)

An HPA is only a controller loop; it reads current metrics from the
**metrics API**, normally served by **metrics-server** — the same
component behind `kubectl top`. If that isn't installed (as may be the
case in this lab), the HPA object exists and is valid, but it will never
scale on CPU because it can never read CPU. That's a configuration you'll
meet in real clusters, and now you can diagnose it in seconds:

```bash
kubectl top pods            # errors → metrics-server missing
kubectl describe hpa <name> # ScalingActive False, FailedGetResourceMetric
```

## The scaling math

```
desiredReplicas = ceil( currentReplicas * (currentUtilization / targetUtilization) )
```

...clamped between `minReplicas` and `maxReplicas`, with stabilization
windows to avoid flapping.

## Beyond CPU

`autoscaling/v2` also scales on memory, on **custom** metrics (e.g.
requests-per-second via an adapter), and on **external** metrics (queue
depth, etc.). And for scale-to-zero and event-driven workloads, teams
often reach for KEDA, which drives an HPA under the hood.

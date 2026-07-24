# Inspect the target metric

An HPA does nothing useful without a **target** to steer toward. For CPU,
the target is expressed as an *average utilization percentage* across the
pods — the controller adds replicas when the observed average is above it
and removes replicas when it's below (subject to min/max).

The modern `autoscaling/v2` API stores this under `spec.metrics[]`:

```bash
kubectl get hpa cpu-worker \
  -o jsonpath='{.spec.metrics[?(@.resource.name=="cpu")].resource.target.averageUtilization}{"\n"}'
```

That should print `50`. If you created the HPA with the older
`autoscaling/v1` object, the same value lives at
`.spec.targetCPUUtilizationPercentage` instead — this step accepts either.

See the whole target inline:

```bash
kubectl describe hpa cpu-worker | sed -n '/Metrics:/,/Min replicas:/p'
```

The scaling math, roughly:

```
desiredReplicas = ceil( currentReplicas * (currentUtilization / targetUtilization) )
```

So with a target of `50%`, one replica sitting at `100%` would push the
HPA toward two replicas — clamped to your `max` of 5. *Whether the HPA can
observe `currentUtilization` at all* is the subject of the next step.

# Tune the autoscaling bounds

Autoscaling policy is not "set once and forget." The `max` is a safety
ceiling: it caps blast radius (cost, downstream load, node pressure) if a
metric spikes or the target is misconfigured. Tightening it is a routine
production change.

Lower the ceiling from 5 to **3**. You can patch in place:

```bash
kubectl patch hpa cpu-worker --type=merge -p '{"spec":{"maxReplicas":3}}'
```

Or edit interactively:

```bash
kubectl edit hpa cpu-worker
```

Confirm the new bound:

```bash
kubectl get hpa cpu-worker -o jsonpath='min={.spec.minReplicas} max={.spec.maxReplicas}{"\n"}'
```

You should now see `min=1 max=3`.

Finally, write down **how you would confirm scaling actually happened** if
this cluster had metrics — the signals you'd watch in a real incident:

```bash
cat > /tmp/scaling-signal <<'EOF'
Watch: `kubectl get hpa cpu-worker -w` (REPLICAS climbing toward max),
`kubectl get deploy cpu-worker -w` (readyReplicas), the HPA's
SuccessfulRescale Events (`kubectl describe hpa`), and `kubectl top pods`
utilization dropping back toward the target as replicas are added.
EOF
cat /tmp/scaling-signal
```

The verify checks that `maxReplicas` is `3` and that your note mentions a
real scaling signal (replicas, events, top, describe, or metric).

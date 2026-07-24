# Read the status — and find out if metrics even exist

Creating an HPA object is not the same as autoscaling *working*. The HPA
controller needs a **metrics source** to read current CPU from. In most
clusters that source is `metrics-server` (the same component that powers
`kubectl top`). In this lab environment it **may or may not be
installed** — and that is exactly the failure mode worth learning to
recognize.

First, look at what the HPA reports:

```bash
kubectl get hpa cpu-worker
kubectl describe hpa cpu-worker | sed -n '/Metrics:/,/Events:/p'
```

Two things to notice:

- **TARGETS** column: a healthy metrics pipeline shows something like
  `cpu: 90%/50%`. A missing one shows `cpu: <unknown>/50%`.
- **Conditions / Events**: without metrics you'll see
  `ScalingActive: False` with reason `FailedGetResourceMetric` — the
  controller is honest that it can't measure CPU, so it won't scale.

Now probe the metrics source directly:

```bash
kubectl top pods -l app=cpu-worker
```

If that prints CPU/memory, metrics-server is present. If it errors with
something like `Metrics API not available` or `error from server (NotFound)`,
it isn't.

Record what you observed (this step just wants your finding, not a
particular outcome):

```bash
# Write "available" or "unavailable" — whichever you saw:
echo "metrics unavailable: kubectl top errored / TARGETS shows <unknown>" > /tmp/metrics-status
cat /tmp/metrics-status
```

Either way, note that `.status.currentReplicas` is populated as soon as
the controller reconciles the object — that field reflects the *current*
state, not a metric, so it works regardless.

```bash
kubectl get hpa cpu-worker -o jsonpath='currentReplicas={.status.currentReplicas}{"\n"}'
```

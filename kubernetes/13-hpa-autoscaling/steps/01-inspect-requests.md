# Autoscaling starts with a CPU request

A **HorizontalPodAutoscaler** (HPA) watches a metric and changes a
Deployment's replica count to keep that metric near a target. The most
common metric is *CPU utilization* — but "utilization" is a percentage,
and a percentage of *what*?

The answer: a percentage of the container's **CPU request**. If a
container requests `100m` (0.1 of a core) and is currently burning `50m`,
that's `50%` utilization. Without a request, the HPA has no denominator
and CPU-based scaling simply does not work.

A Deployment named `cpu-worker` is already running. Confirm it requests
CPU:

```bash
kubectl get deploy cpu-worker
kubectl get deploy cpu-worker \
  -o jsonpath='{.spec.template.spec.containers[0].resources.requests.cpu}{"\n"}'
```

You should see `100m`. Stash it so this scenario can check your progress:

```bash
kubectl get deploy cpu-worker \
  -o jsonpath='{.spec.template.spec.containers[0].resources.requests.cpu}' \
  > /tmp/cpu-request
cat /tmp/cpu-request; echo
```

The container runs a small busy loop, so it will actually consume CPU —
useful later *if* the cluster can measure it.

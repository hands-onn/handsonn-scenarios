# A Service is only as good as its Endpoints

A Service is really two objects working together:

1. The **Service** — a stable ClusterIP + DNS name.
2. Its **Endpoints** — the list of pod IPs the Service currently forwards
   to. Kubernetes rebuilds this list continuously from the pods whose
   labels match the Service's `selector`.

If Endpoints is empty, the ClusterIP has nowhere to send traffic and every
request hangs — exactly what you saw. Look:

```bash
kubectl get svc orders
kubectl get endpoints orders
kubectl describe svc orders | grep -i endpoints
```

Compare that to a healthy Service: `Endpoints` would list two pod IPs
(one per orders replica). Here it shows `<none>` — the Service is
connected to zero pods.

Record the finding so the scenario can track your progress:

```bash
echo "orders Service has empty (no) endpoints" > /tmp/diagnosis
```

Empty Endpoints has one overwhelmingly common cause. Next step: find it.

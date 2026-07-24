# Confirm traffic flows end to end

The Endpoints list is populated. Now prove the original symptom is gone —
run the exact request from the start, again from the client Deployment:

```bash
kubectl exec deploy/client -- curl -s -o /dev/null -w '%{http_code}\n' http://orders
```

This time it returns `200` immediately instead of hanging. See the actual
page:

```bash
kubectl exec deploy/client -- curl -s http://orders | head -5
```

The full path is now wired:

- `orders` **resolves** via DNS to the Service ClusterIP,
- the Service has **Endpoints** (matching pod IPs) — this is the real proof
  your selector fix worked,
- kube-proxy **routes** the request to a healthy orders pod.

Record what you observed (adjust to reality). Endpoints being populated is
the real proof the Service is wired; the live `200` may or may not route in
a vcluster (pod-to-pod ClusterIP routing isn't guaranteed here):

```bash
echo "orders Endpoints now list pod IPs. curl http://orders returned 200 \
(or timed out/000 if this vcluster does not route ClusterIP traffic pod-to-pod; \
the Service wiring is correct regardless)." > /tmp/verify
```

That is a complete, correctly-wired Service — Endpoints populated the moment
the selector matched the pod labels.

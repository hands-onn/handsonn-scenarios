# Service DNS, debugged.

The request hung, but DNS was never the problem. The chain of trust
behind every Service call:

**DNS name → ClusterIP → Endpoints → pod.**

You walked it end to end:

1. **Reproduced** the hang with `kubectl exec deploy/client -- curl http://orders`,
   and proved DNS *resolved* (`nslookup orders` returned a ClusterIP). So the
   break was downstream of DNS.
2. **`kubectl get endpoints orders`** showed `<none>` — the Service pointed
   at zero pods. A ClusterIP with no Endpoints is a black hole: connections
   just time out.
3. **Compared selector to labels.** The Service selected `app=order` while
   the pods were `app=orders`. Kubernetes never errors on a selector that
   matches nothing — it silently selects nothing.
4. **Fixed the selector** to `app=orders`. Endpoints repopulated instantly,
   with no pod restart.
5. **Confirmed** `curl http://orders` returns `200`.

The debugging reflex for "my Service returns nothing / hangs":

```bash
kubectl get endpoints <svc>          # empty? → selector/label mismatch or no ready pods
kubectl get svc <svc> -o jsonpath='{.spec.selector}'
kubectl get pods --show-labels       # do the labels actually match the selector?
```

Related gotchas that also empty out Endpoints, worth knowing:

- Pods exist and are labelled right, but aren't **Ready** (failing readiness
  probe) — Endpoints only lists Ready pods.
- The Service's `targetPort` doesn't match the container's real port.
- Right labels, wrong **namespace** — a Service only selects pods in its own
  namespace.

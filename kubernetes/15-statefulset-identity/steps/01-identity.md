# The StatefulSet identity contract

A **Deployment** treats its pods as interchangeable — random name
suffixes, one shared Service VIP, no per-pod storage. A **StatefulSet**
is the opposite: every replica gets a *stable, sticky identity* made of
three things that stay put across restarts and rescheduling:

1. a **stable name** — `<statefulset>-<ordinal>` (`db-0`, `db-1`, `db-2`),
2. a **stable network identity** — its own DNS record via a headless
   Service,
3. **stable storage** — its own PVC from a `volumeClaimTemplate`.

That's what databases, queues, and consensus systems need: `db-0` must
come back as `db-0`, on the same disk, reachable at the same address.

A StatefulSet named `db` was created for you, fronted by a headless
Service (also `db`). Look at the two fields that wire it together:

```bash
kubectl get statefulset db -o yaml | grep -A1 -E 'serviceName|replicas:'
```

- `spec.serviceName: db` pins the StatefulSet to the headless Service
  that governs its pods' DNS. This is mandatory — it's the "governing
  Service".
- `spec.replicas: 3` asks for three ordered pods.

Confirm both are set:

```bash
kubectl get statefulset db -o jsonpath='serviceName={.spec.serviceName} replicas={.spec.replicas}{"\n"}'
```

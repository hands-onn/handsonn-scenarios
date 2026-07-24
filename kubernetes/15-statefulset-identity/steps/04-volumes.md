# Each pod gets its own volume

Stable identity #3 is storage. A StatefulSet doesn't share one PVC across
replicas — it has a **`volumeClaimTemplate`**, and the controller stamps
out one PVC *per pod* from it, named `<template>-<statefulset>-<ordinal>`.

Look at the template:

```bash
kubectl get statefulset db -o jsonpath='{.spec.volumeClaimTemplates[0].metadata.name}{"\n"}'
```

That template name is `data`, so the generated PVCs are:

```bash
kubectl get pvc -l app=db
```

Expect `data-db-0`, `data-db-1`, `data-db-2` — one sticky disk each.
`db-1` will always mount `data-db-1`; that's how a database pod finds its
own data after a restart.

Check the phase:

```bash
kubectl get pvc -o custom-columns=NAME:.metadata.name,PHASE:.status.phase
```

> **Heads up — Pending is acceptable here.** These PVCs only reach
> `Bound` if the cluster has a default StorageClass and its binding mode
> provisions the volume. Many clusters use `WaitForFirstConsumer` (binds
> when the pod schedules), and some sessions have **no** default
> StorageClass at all — in which case the PVCs stay `Pending`
> indefinitely. The check verifies the PVC **objects exist** with the
> right per-ordinal names (that's the identity guarantee) and only
> *reports* Bound/Pending; it will **not** fail on Pending.

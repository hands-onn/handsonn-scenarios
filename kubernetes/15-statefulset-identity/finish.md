# StatefulSet stable identity, in one picture

You dissected the three guarantees that make a StatefulSet different from
a Deployment — and watched them survive a pod delete.

The identity contract:

- **Stable name** — pods are `<sts>-<ordinal>` (`db-0`, `db-1`, `db-2`),
  created/scaled in order, never random suffixes. `db-1` is always
  `db-1`.
- **Stable network identity** — the **headless Service**
  (`clusterIP: None`) named in `spec.serviceName` gives each pod its own
  DNS record: `db-0.db.<ns>.svc.cluster.local`. No shared VIP; you can
  address a specific replica.
- **Stable storage** — a `volumeClaimTemplate` mints one PVC per pod
  (`data-db-0/1/2`). The PVC is *not* deleted with the pod, so a
  recreated `db-1` reattaches `data-db-1` and keeps its data.

Why it matters: databases, Kafka, ZooKeeper, etcd, and other clustered
stateful systems assume "node N always comes back as node N, on its own
disk, at its own address." That's precisely what a Deployment can't
promise and a StatefulSet does.

Field cheat-sheet:

| Field | Purpose |
|---|---|
| `spec.serviceName` | Governing **headless** Service for per-pod DNS (required) |
| `spec.replicas` | Number of ordered pods (db-0 … db-N) |
| `spec.volumeClaimTemplates[]` | One PVC per pod, named `<tmpl>-<sts>-<ordinal>` |
| `spec.podManagementPolicy` | `OrderedReady` (default: db-0 must be Ready before db-1 starts) vs `Parallel` (all at once). This lab uses **Parallel** so pods appear even if PVCs stay Pending without a default StorageClass. |
| `spec.updateStrategy` | `RollingUpdate` (reverse-ordinal) vs `OnDelete` |

Operational notes:
- Deleting the StatefulSet does **not** delete its PVCs by default —
  intentional, so you don't lose data. Clean them up explicitly.
- Scale-down removes highest ordinals first (`db-2`, then `db-1`); the
  freed PVCs are kept for when you scale back up.
- `WaitForFirstConsumer` volumes and clusters without a default
  StorageClass leave PVCs `Pending` — the *names and wiring* are still
  correct; only the actual disk provisioning waits.

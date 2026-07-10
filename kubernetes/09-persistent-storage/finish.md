# Storage that survives.

The abstraction chain:

- **StorageClass** — a *type* of storage the cluster can provision.
- **PVC (PersistentVolumeClaim)** — your *request* ("100Mi, RWO").
- **PV (PersistentVolume)** — the *actual* volume, provisioned to satisfy
  the claim (dynamically, via the StorageClass).
- Pods mount the **PVC**; they never reference the PV directly.

Why it matters: pods are cattle — deleted and recreated constantly. Data
in a pod's own filesystem dies with it. A PVC decouples data lifecycle
from pod lifecycle, so databases, uploads, and caches survive restarts,
rescheduling, and rolling updates.

Things to know:
- **accessModes**: `ReadWriteOnce` (one node — most common),
  `ReadWriteMany` (needs NFS/CephFS-type backend).
- **`WaitForFirstConsumer`**: PVC stays Pending until a pod uses it, so
  the volume lands on the right node. Normal, not a bug.
- **reclaimPolicy** on the PV: `Delete` (default for dynamic — volume
  removed with the PVC) vs `Retain` (kept for manual recovery).
- For a set of pods each needing their own stable volume, use a
  **StatefulSet** with `volumeClaimTemplates`.

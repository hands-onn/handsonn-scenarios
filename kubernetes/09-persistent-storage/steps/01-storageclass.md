# Storage starts with a StorageClass

A **StorageClass** describes a *kind* of storage the cluster can
provision on demand (SSD, network disk, local path, ...). See what's
available:

```bash
kubectl get storageclass
```

There's a default one (marked `(default)`). When you create a PVC without
naming a class, the default is used to dynamically provision a volume —
no manual PV creation needed.

The flow you're about to use:
**PVC** (I want storage) → StorageClass provisions a **PV** (actual
volume) → pod mounts the PVC. You ask for storage abstractly; the cluster
supplies it.

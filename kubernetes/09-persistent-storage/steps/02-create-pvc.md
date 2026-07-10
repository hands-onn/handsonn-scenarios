# Claim some storage

A **PersistentVolumeClaim** is a request for storage of a given size and
access mode. Create one:

```bash
kubectl apply -f - <<'YAML'
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: data
spec:
  accessModes: ["ReadWriteOnce"]
  resources:
    requests:
      storage: 100Mi
YAML
```

Check it:

```bash
kubectl get pvc data
```

You may see it as `Pending` at first — many provisioners (including the
default `local-path` here) use `WaitForFirstConsumer`, meaning the volume
isn't actually created until a pod mounts the PVC. That's normal; it
binds in the next step.

`accessModes`: `ReadWriteOnce` (one node), `ReadOnlyMany`,
`ReadWriteMany` (needs a shared filesystem backend).

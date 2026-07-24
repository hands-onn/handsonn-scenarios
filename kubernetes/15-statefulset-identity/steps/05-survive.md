# Prove the identity is sticky

Time to see the whole contract hold under failure. Delete the middle
replica:

```bash
kubectl delete pod db-1
```

Now watch the StatefulSet controller react:

```bash
kubectl get pods -l app=db -w
```

Two things happen that a Deployment would *not* do:

- The replacement pod comes back with the **same name** — `db-1`, not a
  new random name. (Press Ctrl-C to stop the watch once it reappears.)
- It reattaches the **same volume** — PVC `data-db-1` is *not* deleted
  when the pod is deleted, so the new `db-1` mounts the exact disk the
  old one used. Its data (identity #3) survives.

Verify the recreated pod still points at its original claim:

```bash
kubectl get pod db-1 -o jsonpath='{.spec.volumes[?(@.name=="data")].persistentVolumeClaim.claimName}{"\n"}'
```

That must print `data-db-1`. Stable name + stable storage = the same
"database node" came back, exactly where it left off.

> If `db-1` is slow to reappear (e.g. its PVC is `Pending` for lack of a
> StorageClass), the pod is still *created* with the right name and claim
> reference immediately — that's what the check looks at. Give it a few
> seconds and re-run the check.

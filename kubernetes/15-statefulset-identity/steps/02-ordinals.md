# Ordered, deterministic names

Unlike a Deployment's `api-7d9f8c-abcde` random pods, a StatefulSet names
its pods by **ordinal index**, starting at 0:

```bash
kubectl get pods -l app=db
```

You should see `db-0`, `db-1`, `db-2`. Two rules make these predictable:

- **Ordered creation**: by default pods are created one at a time, in
  order — `db-0` becomes Ready before `db-1` starts, and so on. (Scaling
  down happens in reverse: `db-2` first.)
- **Stable names**: the ordinal is part of the pod's identity, not a
  random suffix. `db-1` is *always* `db-1`.

Each pod's hostname matches its pod name — check one:

```bash
kubectl exec db-0 -- hostname
```

Every pod carries an `ownerReferences` entry pointing back at the
StatefulSet that controls it. Confirm `db-0` is owned by a StatefulSet:

```bash
kubectl get pod db-0 -o jsonpath='{.metadata.ownerReferences[0].kind}/{.metadata.ownerReferences[0].name}{"\n"}'
```

> Note: with no default StorageClass, a pod can sit in `Pending` waiting
> on its volume. That's fine here — the **name** `db-1` is assigned the
> moment the pod is created, regardless of whether it's Running yet.

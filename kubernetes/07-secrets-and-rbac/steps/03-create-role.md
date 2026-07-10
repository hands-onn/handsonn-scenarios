# A Role: namespaced permissions

RBAC has two halves: a **Role** (what actions are allowed) and a
**RoleBinding** (who gets them). A Role is namespaced — it grants verbs
on resources within one namespace.

Create a Role that can only read Secrets:

```bash
kubectl create role secret-reader \
  --verb=get --verb=list \
  --resource=secrets
```

Inspect it:

```bash
kubectl get role secret-reader -o yaml
```

`rules` lists `apiGroups`, `resources`, and `verbs`. This Role grants
`get`/`list` on `secrets` and nothing else — no create, no delete, no
other resource types. Least privilege by construction.

(ClusterRole is the cluster-scoped equivalent, for non-namespaced
resources or cluster-wide grants.)

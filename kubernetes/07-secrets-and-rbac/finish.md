# Secrets + RBAC wired.

Two separate concerns you connected:

**Secrets** — hold sensitive values, injected as env vars or mounted
files. Remember:
- base64 ≠ encryption. Control access with RBAC; enable
  encryption-at-rest on the cluster for real protection.
- File mounts update live; env vars are frozen at pod start.

**RBAC** — who can do what:
- **Role** / **ClusterRole** — a set of allowed (apiGroup, resource,
  verb) rules. Additive only; there's no "deny" rule.
- **RoleBinding** / **ClusterRoleBinding** — grant a Role to subjects
  (users, groups, ServiceAccounts).
- **`kubectl auth can-i <verb> <resource> --as=<subject>`** — the
  indispensable tool for testing grants without trial-and-error deploys.

Least-privilege pattern: one ServiceAccount per workload, bound to the
narrowest Role that workload actually needs. Never hand pods
`cluster-admin` "to make it work."

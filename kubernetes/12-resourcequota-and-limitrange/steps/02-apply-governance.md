# Two objects, two jobs

Namespace governance is a pair, and it's easy to confuse them:

- **ResourceQuota** — a *namespace-wide budget*. The sum of all pods'
  requests (and the pod count) can't exceed these caps. Enforced at
  admission: once the budget is full, the next pod is **rejected**.
- **LimitRange** — *per-container* policy. Sets default requests/limits
  for containers that omit them, and can enforce min/max per container.

They interlock: a ResourceQuota that caps `requests.cpu`/`requests.memory`
**requires** every container to declare those requests, or the pod is
rejected. The LimitRange's job is to fill those in automatically so app
teams don't have to.

Apply both to `team-a`:

```bash
kubectl apply -f - <<'EOF'
apiVersion: v1
kind: ResourceQuota
metadata:
  name: team-quota
  namespace: team-a
spec:
  hard:
    requests.cpu: "500m"
    requests.memory: "512Mi"
    pods: "4"
---
apiVersion: v1
kind: LimitRange
metadata:
  name: team-limits
  namespace: team-a
spec:
  limits:
    - type: Container
      defaultRequest:      # injected when a container omits requests
        cpu: "150m"
        memory: "128Mi"
      default:             # injected when a container omits limits
        cpu: "300m"
        memory: "256Mi"
EOF
```

Verify the objects landed, and read the quota's live accounting:

```bash
kubectl -n team-a describe resourcequota team-quota
kubectl -n team-a describe limitrange team-limits
```

The `describe` on the quota shows a **Used / Hard** table. Right now the
already-running `shop` pod predates the LimitRange, so its `Used` may show
`0` for cpu/memory — we fix that next.

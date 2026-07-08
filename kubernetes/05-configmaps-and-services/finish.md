# Config + discovery wired.

What you connected:

- **ConfigMap** — non-secret config as key/values, decoupled from the
  image. (Secrets are the same shape for sensitive data, base64-encoded
  and mountable with tighter RBAC.)
- **envFrom / env.valueFrom.configMapKeyRef** — how a pod consumes it.
  Note: env vars are captured at pod start; changing the ConfigMap does
  **not** update running pods (you must restart them). Mounted-as-volume
  ConfigMaps *do* update live, with a delay.
- **Service** — stable ClusterIP + DNS in front of label-selected pods.
- **Endpoints** — the auto-maintained list of pod IPs behind a Service.
  Empty endpoints = selector mismatch.
- **Cluster DNS** — `<svc>`, `<svc>.<ns>`, `<svc>.<ns>.svc.cluster.local`
  all resolve. Cross-namespace calls need at least `<svc>.<ns>`.

This is the backbone of every microservice deployment on Kubernetes.

# Survey the backends

An **Ingress** is a routing rule set — it says "HTTP for path `/a` goes to
Service X, path `/b` goes to Service Y." It routes to **Services**, never
directly to pods, so before writing one you confirm the Services exist and
actually have endpoints.

Two apps are pre-seeded: `app-a` and `app-b`, each an `nginx:1.27`
Deployment fronted by a same-named ClusterIP Service on port 80.

Look at what you have:

```bash
kubectl get deploy,svc
kubectl get endpoints app-a app-b
```

Both Deployments should read `1/1`, and each Service's `Endpoints` should
list a pod IP. If a Service has empty endpoints its selector doesn't match
any pod — routing to it would 503 even with a perfect Ingress.

Confirm the Services answer inside the cluster (Ingress only adds an L7
edge; the ClusterIPs already work):

```bash
kubectl run probe --rm -it --image=busybox:1.36 --restart=Never -- \
  wget -qO- http://app-a | head -3
```

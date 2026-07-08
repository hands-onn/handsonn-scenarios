# Scale the Deployment

One replica is a single point of failure. Scale `web` to 3:

```bash
kubectl scale deployment web --replicas=3
```

Watch the new Pods appear:

```bash
kubectl get pods -w
```

Press Ctrl-C to stop watching. You should now have 3 `web-*` Pods. The
Deployment controller noticed the desired count changed and created two
more Pods to match.

Declarative alternative (what you'd do in real life via GitOps):

```bash
kubectl edit deployment web    # change spec.replicas
# or
kubectl patch deployment web -p '{"spec":{"replicas":3}}'
```

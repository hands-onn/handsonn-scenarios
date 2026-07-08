# A normal version bump

Update the image to a newer nginx. This is the everyday deploy:

```bash
kubectl set image deployment/web web=nginx:1.27
```

Watch it roll out — Kubernetes does a rolling update by default, bringing
up new pods before tearing down old ones:

```bash
kubectl rollout status deployment/web
kubectl get pods
```

Check the history now — you'll see revision 2:

```bash
kubectl rollout history deployment/web
```

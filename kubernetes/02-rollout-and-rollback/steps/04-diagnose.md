# See the rollout wedge

```bash
kubectl rollout status deployment/web --timeout=20s
```

It won't complete — it'll time out. Look at why:

```bash
kubectl get pods
kubectl describe deployment web | tail -20
```

You'll see new `web-*` pods in `ImagePullBackOff` / `ErrImagePull`, while
the old replica set still serves traffic. `kubectl get rs` shows both
ReplicaSets — the old one still at 3 healthy, the new one unable to
progress.

When you've seen it, mark the step:

```bash
touch /tmp/diagnosed
```

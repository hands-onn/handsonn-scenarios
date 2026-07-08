# Two broken Deployments

`puller` and `picky` were both created but neither has a running pod.
They're broken in different ways — your job is to tell them apart.

```bash
kubectl get pods
```

You'll see two pods stuck in different states:
- one in `ImagePullBackOff` / `ErrImagePull`
- one in `Pending`

`describe` each to see the reason:

```bash
kubectl describe pod -l app=puller | tail -15
kubectl describe pod -l app=picky  | tail -15
```

- **puller**: the Events show `Failed to pull image ... not found`.
- **picky**: the Events show `FailedScheduling ... 0/1 nodes are
  available: ... node(s) didn't match node selector`.

Different failure classes: one is a *pull* problem (before the container
can run), the other is a *scheduling* problem (the pod can't even be
placed on a node). When you've seen both:

```bash
touch /tmp/triaged
```

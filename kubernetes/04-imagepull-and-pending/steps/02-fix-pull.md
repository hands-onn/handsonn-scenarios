# Fix the image pull

`puller` references `nginx:1.25-typo-nonexistent` — no such tag exists on
the registry, so the kubelet can't pull it. Point it at a real image:

```bash
kubectl set image deployment/puller app=nginx:1.27
```

Watch it recover:

```bash
kubectl get pods -l app=puller -w
```

`ImagePullBackOff` almost always means one of:
- typo in the image name or tag (this lab)
- the tag was deleted from the registry
- private registry with no / wrong `imagePullSecret`
- rate-limited by the registry (Docker Hub anonymous pulls)

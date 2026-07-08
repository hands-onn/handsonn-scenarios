# Now break it

Deploy an image tag that doesn't exist — the classic "typo in the tag"
outage:

```bash
kubectl set image deployment/web web=nginx:does-not-exist
```

Kubernetes will try to pull it, fail, and the new pods will get stuck in
`ImagePullBackOff`. Crucially, the **old pods keep running** — a rolling
update won't kill healthy pods until new ones are ready. That's the
safety net.

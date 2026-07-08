# Why is it crashing?

Two tools, always in this order:

**1. Logs** — what did the container say before it died?

```bash
kubectl logs $(cat /tmp/podname)
# If it already restarted, see the PREVIOUS container's logs:
kubectl logs $(cat /tmp/podname) --previous
```

**2. Describe** — what does Kubernetes see (exit code, events)?

```bash
kubectl describe pod $(cat /tmp/podname) | sed -n '/Containers:/,/Events:/p'
kubectl describe pod $(cat /tmp/podname) | tail -15
```

Look at `Last State: Terminated`, `Reason: Error`, `Exit Code: 1`. The
container prints `starting` then exits 1 — its command is broken.

Record the root cause:

```bash
echo "container command exits 1 immediately" > /tmp/rootcause
```

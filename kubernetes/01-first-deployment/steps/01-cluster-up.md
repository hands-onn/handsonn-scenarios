# Your own Kubernetes cluster

This lab gives you a **real, isolated Kubernetes cluster** — not a shared
one. You're cluster-admin. Anything you break only affects you, and it's
all thrown away when the session ends.

`kubectl` is already configured. Confirm you can reach the cluster:

```bash
kubectl cluster-info
kubectl get nodes
kubectl get namespaces
```

You'll see the API server responding and the built-in namespaces
(`default`, `kube-system`, etc.). When it works, click **Check**.

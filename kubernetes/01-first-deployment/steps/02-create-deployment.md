# Create a Deployment

A **Deployment** manages a set of identical Pods and keeps them running.
Create one named `web` running the nginx image:

```bash
kubectl create deployment web --image=nginx
```

Watch it come up:

```bash
kubectl get deploy
kubectl get pods
```

The Deployment creates a ReplicaSet, which creates a Pod. That chain —
Deployment → ReplicaSet → Pod — is the core of how Kubernetes runs
workloads. See it:

```bash
kubectl get deploy,rs,pods
```

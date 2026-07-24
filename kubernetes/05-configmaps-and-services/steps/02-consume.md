# Inject it into pods

A ConfigMap does nothing until a pod consumes it. The cleanest way is
`envFrom`, which turns every key into an environment variable.

Apply a Deployment that pulls all of app-config into its env:

```bash
kubectl apply -f - <<'YAML'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
  labels: { app: web }
spec:
  replicas: 2
  selector:
    matchLabels: { app: web }
  template:
    metadata:
      labels: { app: web }
    spec:
      containers:
        - name: web
          image: nginx:1.27
          envFrom:
            - configMapRef:
                name: app-config
          ports:
            - containerPort: 80
YAML
```

Verify the env landed inside a pod. You can `exec` straight into the
Deployment — kubectl picks a ready pod for you, no name-copying needed:

```bash
kubectl rollout status deployment/web
kubectl exec deploy/web -- printenv GREETING
```

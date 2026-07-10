# Consume the Secret in a pod

Apply a Deployment that injects `API_KEY` from the Secret as an env var:

```bash
kubectl apply -f - <<'YAML'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: reader
  labels: { app: reader }
spec:
  replicas: 1
  selector:
    matchLabels: { app: reader }
  template:
    metadata:
      labels: { app: reader }
    spec:
      containers:
        - name: reader
          image: busybox:1.36
          command: ["sh","-c","echo key is $API_KEY; sleep 3600"]
          env:
            - name: API_KEY
              valueFrom:
                secretKeyRef:
                  name: app-secret
                  key: API_KEY
YAML
```

Verify the value reached the container:

```bash
kubectl rollout status deployment/reader
kubectl logs deploy/reader
```

You can also mount Secrets as files (`volumes: [{secret: ...}]`) — better
for certs/keys, and file mounts update live when the Secret changes,
unlike env vars.

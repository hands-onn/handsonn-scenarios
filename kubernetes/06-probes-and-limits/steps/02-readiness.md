# Readiness: "can I take traffic yet?"

A **readiness probe** gates traffic. A pod is only added to its Service's
endpoints once the probe passes — and removed if it later fails, without
restarting the container.

Add an httpGet readiness probe to `web`:

```bash
kubectl patch deployment web --type=json -p='[
  {"op":"add","path":"/spec/template/spec/containers/0/readinessProbe","value":{
    "httpGet":{"path":"/","port":80},
    "initialDelaySeconds":2,
    "periodSeconds":5
  }}
]'
```

Watch the rollout — pods now only become `READY` once the probe passes:

```bash
kubectl rollout status deployment/web
kubectl get pods -l app=web
```

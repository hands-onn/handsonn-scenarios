# Externalize config

Hard-coding config into images is an anti-pattern. A **ConfigMap** holds
non-secret config as key/value pairs, decoupled from the image.

Create one named `app-config` with a greeting:

```bash
kubectl create configmap app-config \
  --from-literal=GREETING="hello from handsonn" \
  --from-literal=TIER="dev"
```

Inspect it:

```bash
kubectl get configmap app-config -o yaml
```

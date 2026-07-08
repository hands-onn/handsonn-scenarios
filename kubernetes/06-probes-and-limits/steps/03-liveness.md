# Liveness: "am I still healthy?"

A **liveness probe** restarts a container that's stopped working —
deadlocked, hung, wedged — even though its process is still alive.

Add one on port 80:

```bash
kubectl patch deployment web --type=json -p='[
  {"op":"add","path":"/spec/template/spec/containers/0/livenessProbe","value":{
    "httpGet":{"path":"/","port":80},
    "initialDelaySeconds":5,
    "periodSeconds":10,
    "failureThreshold":3
  }}
]'
```

Readiness vs liveness — the distinction that trips everyone up:
- **Readiness fails** → pod removed from Service (no traffic), container
  keeps running. Recovers on its own when the probe passes again.
- **Liveness fails** (failureThreshold times) → container **killed and
  restarted**.

Set liveness too aggressively and you'll restart-loop a healthy-but-slow
app. `initialDelaySeconds` must cover your app's startup time (or use a
`startupProbe`).

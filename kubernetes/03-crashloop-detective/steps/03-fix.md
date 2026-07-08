# Fix the command

The container's command is `sh -c "echo starting && exit 1"` — it
deliberately exits. A long-running container needs a command that
doesn't return. Patch the Deployment to sleep instead:

```bash
kubectl set env deployment/api --list >/dev/null 2>&1   # (no-op; just showing kubectl)
kubectl patch deployment api --type=json -p='[
  {"op":"replace","path":"/spec/template/spec/containers/0/command","value":["sh","-c","echo starting && sleep infinity"]}
]'
```

Or edit interactively and change the command:

```bash
kubectl edit deployment api
```

Watch it recover:

```bash
kubectl rollout status deployment/api
kubectl get pods -l app=api
```

The new pod should reach `Running` with `0` restarts.

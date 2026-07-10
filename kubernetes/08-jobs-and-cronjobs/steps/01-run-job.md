# A Job runs to completion

A Deployment keeps pods running forever. A **Job** runs a pod until its
task *succeeds* (exit 0), then stops — for batch work: migrations,
imports, one-off scripts.

```bash
kubectl apply -f - <<'YAML'
apiVersion: batch/v1
kind: Job
metadata:
  name: hello
spec:
  template:
    spec:
      restartPolicy: Never
      containers:
        - name: hello
          image: busybox:1.36
          command: ["sh","-c","echo processing...; sleep 3; echo done"]
YAML
```

Watch it complete:

```bash
kubectl get job hello -w
```

`COMPLETIONS` goes `0/1` → `1/1`. Ctrl-C to stop watching. Unlike a
Deployment, the pod stays `Completed` (not restarted) once done.

`restartPolicy: Never` (or `OnFailure`) is required for Jobs — `Always`
isn't allowed, since a Job isn't meant to run forever.

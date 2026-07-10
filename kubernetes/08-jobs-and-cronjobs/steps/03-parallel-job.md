# Jobs with multiple completions

`completions: N` means "run the pod to success N times". `parallelism: M`
controls how many run at once. Useful for work-queue / batch patterns.

```bash
kubectl apply -f - <<'YAML'
apiVersion: batch/v1
kind: Job
metadata:
  name: batch
spec:
  completions: 3
  parallelism: 2
  template:
    spec:
      restartPolicy: Never
      containers:
        - name: worker
          image: busybox:1.36
          command: ["sh","-c","echo working; sleep 2"]
YAML
```

Watch three completions accrue, two pods at a time:

```bash
kubectl get job batch -w
kubectl get pods --selector=job-name=batch
```

`COMPLETIONS` climbs `0/3` → `3/3`. `backoffLimit` (default 6) caps
retries before the Job is marked failed.

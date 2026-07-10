# Where did the work happen?

A Job creates a pod (with a generated name). Find it and read its logs:

```bash
kubectl get pods --selector=job-name=hello
kubectl logs job/hello
```

`kubectl logs job/hello` conveniently targets the Job's pod. You'll see
`processing...` and `done`.

Inspect the Job's status details:

```bash
kubectl describe job hello | tail -15
```

Note `Completions`, `Duration`, and the pod events. When you've read the
output:

```bash
touch /tmp/job_output
```

Completed Job pods linger (so you can read logs) until you delete the
Job or set `ttlSecondsAfterFinished` for auto-cleanup.

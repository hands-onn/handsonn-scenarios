# Schedule it with a CronJob

A **CronJob** creates a Job on a cron schedule — the Kubernetes-native
equivalent of a crontab entry, for recurring batch work (backups,
reports, cleanup).

```bash
kubectl apply -f - <<'YAML'
apiVersion: batch/v1
kind: CronJob
metadata:
  name: heartbeat
spec:
  schedule: "* * * * *"          # every minute
  jobTemplate:
    spec:
      template:
        spec:
          restartPolicy: Never
          containers:
            - name: beat
              image: busybox:1.36
              command: ["sh","-c","date; echo heartbeat"]
YAML
```

Inspect it and wait for it to spawn a Job:

```bash
kubectl get cronjob heartbeat
kubectl get jobs -w        # a heartbeat-<timestamp> Job appears each minute
```

Same 5-field cron syntax as classic cron. `successfulJobsHistoryLimit` /
`failedJobsHistoryLimit` control how many finished Jobs it keeps;
`concurrencyPolicy: Forbid` prevents overlapping runs.

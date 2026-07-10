# Batch and scheduled workloads.

| Kind | Runs | For |
|---|---|---|
| Deployment | Forever (restarts) | Long-lived services |
| Job | Until N successes | One-off / batch tasks |
| CronJob | A Job on a schedule | Recurring batch work |

Job knobs:
- `completions` — how many successful runs are needed.
- `parallelism` — how many pods run concurrently.
- `backoffLimit` — retries before the Job fails.
- `activeDeadlineSeconds` — hard time limit.
- `ttlSecondsAfterFinished` — auto-delete finished Jobs.
- `restartPolicy` must be `Never` or `OnFailure` (never `Always`).

CronJob knobs:
- `schedule` — 5-field cron.
- `concurrencyPolicy` — `Allow` / `Forbid` / `Replace` for overlaps.
- `startingDeadlineSeconds` — skip a run if the controller was down too
  long.
- history limits — how many finished Jobs to retain for debugging.

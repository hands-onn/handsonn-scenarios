# CrashLoopBackOff, solved.

The debugging reflex, every time:

1. **`kubectl get pods`** — status + RESTARTS count points you at the bad pod.
2. **`kubectl logs <pod>`** (and `--previous` if it already restarted) —
   the app's own last words. 80% of the time the answer is here.
3. **`kubectl describe pod <pod>`** — exit code, events, OOMKilled?,
   image pull errors, failed mounts. What Kubernetes itself observed.

CrashLoopBackOff is a *symptom*, not a cause. Common real causes:
- Bad command / missing binary (this lab).
- Missing config/secret the app needs at startup.
- Failing a liveness probe (see probes-and-limits).
- `OOMKilled` — memory limit too low.
- Can't reach a dependency (DB, another service) and exits.

The `BackOff` part just means Kubernetes is spacing out restarts (up to
5 min between tries) — so a fixed deploy may take a moment to retry, or
delete the pod to force an immediate restart.

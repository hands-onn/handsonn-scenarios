# Rollouts, mastered.

The core mechanics:

- **`kubectl set image`** (or editing the spec) triggers a new rollout —
  a new ReplicaSet scaled up while the old scales down.
- **Rolling update** (the default) keeps old pods serving until new ones
  are Ready, so a broken image can't take you fully down — it just wedges
  the rollout.
- **`kubectl rollout status`** blocks until done (or you time out) —
  essential in CI/CD gates.
- **`kubectl rollout history`** + **`kubectl rollout undo`** give you
  instant recovery without hunting for the old manifest.

Production tip: set `spec.strategy.rollingUpdate.maxUnavailable: 0` for
zero-downtime deploys, and always pair a deploy with a readiness probe
(see the probes-and-limits scenario) so Kubernetes knows when a new pod
is truly ready — not just started.

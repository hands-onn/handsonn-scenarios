# Production-ready workloads.

The four things that separate a demo Deployment from a production one:

| Field | Without it | With it |
|---|---|---|
| `readinessProbe` | Traffic hits pods before they can serve | Traffic only to Ready pods; auto-drained when unhealthy |
| `livenessProbe` | Hung pods stay in rotation forever | Wedged containers auto-restarted |
| `resources.requests` | Scheduler guesses; nodes over/under-packed | Predictable placement |
| `resources.limits` | One pod can starve the node | Bounded blast radius (OOMKill / CPU throttle) |

Gotchas worth internalizing:
- **initialDelaySeconds** on liveness must exceed real startup time, or
  you restart-loop a healthy app. Prefer a dedicated `startupProbe` for
  slow starters.
- **requests == limits** for memory gives the most predictable behavior
  (Guaranteed QoS class) — Kubernetes evicts these last under pressure.
- Readiness and liveness should usually hit **different** endpoints in a
  real app: readiness checks dependencies (DB reachable?), liveness only
  checks "is the process wedged?" — so a DB blip drains traffic without
  triggering a pointless restart.

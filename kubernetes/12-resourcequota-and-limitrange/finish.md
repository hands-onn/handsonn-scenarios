# Namespace governance, wired up.

You built the standard two-object guardrail every multi-team cluster
needs, and you watched each piece behave:

| Object | Scope | What it did here |
|---|---|---|
| **ResourceQuota** | Whole namespace | Capped total `requests.cpu`/`requests.memory` and pod count; rejected the pod that would blow the budget |
| **LimitRange** | Per container | Injected `default`/`defaultRequest` into a container that declared nothing, so it could satisfy the quota automatically |

The mental model to keep:

- A ResourceQuota on `requests.*` makes those requests **mandatory** — any
  pod without them is rejected. The LimitRange is what saves you: it fills
  in sane defaults so app teams don't have to annotate every container.
- Both are **admission-time**. Existing pods are never retro-fitted — you
  saw the pre-quota pod keep its empty `resources` until you rolled it.
- Quota rejection surfaces on the **controller** (ReplicaSet `FailedCreate`
  event: `exceeded quota: ...`), not on a pod — because the pod is never
  created. When a Deployment sits below its desired replica count with no
  obvious pod error, check the ReplicaSet events for quota.
- `kubectl describe resourcequota` is your live budget: the **Used / Hard**
  table tells you exactly how much room is left before the next rejection.

Gotchas worth carrying forward:

- If your quota caps `limits.cpu`/`limits.memory` too, the LimitRange must
  provide `default` (limits) as well — otherwise limitless containers get
  rejected the same way.
- `requests == limits` for memory puts a pod in the **Guaranteed** QoS
  class (evicted last). BestEffort (no requests at all) is evicted first —
  which is exactly the state `shop` started in.
- Editing LimitRange defaults changes behavior for **future** pods only.
  Roll the workload to apply them.

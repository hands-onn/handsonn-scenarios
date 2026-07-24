The ResourceQuota goes under `spec.hard` with dotted keys: `requests.cpu`,
`requests.memory`, `pods` (all quoted strings). The LimitRange needs one
entry with `type: Container` carrying both a `default` (limits) and a
`defaultRequest` (requests) map. Both objects are namespaced — don't forget
`namespace: team-a` (or `-n team-a`). Check with
`kubectl -n team-a get resourcequota,limitrange`.

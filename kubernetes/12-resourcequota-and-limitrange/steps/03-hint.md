The old pod won't gain resources retroactively — you must recreate it.
`kubectl -n team-a rollout restart deployment/shop` spins up a fresh pod
that passes through the LimitRange on admission. Then read the **pod**
(`kubectl -n team-a get pod -l app=shop -o jsonpath=...`), not the
Deployment template — the template stays empty; only the live pod carries
the injected `requests`/`limits`.

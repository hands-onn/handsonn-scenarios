# Defaults only apply to NEW pods

A LimitRange is an admission mutation: it stamps defaults onto pods **as
they are created**. The `shop` pod that's already running was admitted
*before* the LimitRange existed, so it still has no resources. Prove that:

```bash
kubectl -n team-a get pod -l app=shop \
  -o jsonpath='{.items[0].spec.containers[0].resources}{"\n"}'
```

Empty `{}`. Now force a fresh pod through the admission path by rolling
the Deployment — note we are **not** editing the container's resources; we
want to prove the LimitRange fills them in for us:

```bash
kubectl -n team-a rollout restart deployment/shop
kubectl -n team-a rollout status deployment/shop
```

Inspect the running pod again. This time the `resources` block is
populated even though the Deployment template still declares nothing:

```bash
kubectl -n team-a get pod -l app=shop \
  -o jsonpath='{.items[0].spec.containers[0].resources}{"\n"}'
```

You should see `requests` of `cpu: 150m / memory: 128Mi` and `limits` of
`cpu: 300m / memory: 256Mi` — exactly the LimitRange defaults. The
Deployment template is still bare:

```bash
kubectl -n team-a get deploy shop \
  -o jsonpath='{.spec.template.spec.containers[0].resources}{"\n"}'
```

That's the whole point: app teams don't have to write resources; the
platform's LimitRange guarantees every container has some, which in turn
satisfies the ResourceQuota. Watch the quota now count real usage:

```bash
kubectl -n team-a get resourcequota team-quota \
  -o jsonpath='USED cpu={.status.used.requests\.cpu} mem={.status.used.requests\.memory} pods={.status.used.pods}{"\n"}'
```

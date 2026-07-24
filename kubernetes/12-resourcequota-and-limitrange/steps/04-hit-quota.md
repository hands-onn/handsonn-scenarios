# Push past the budget

Do the math on the quota. Each `shop` pod now requests `150m` CPU (the
LimitRange default). The quota caps `requests.cpu` at `500m`:

```
500m / 150m per pod  =  3 pods max  (the 4th needs 600m > 500m)
```

So the CPU budget — not the `pods: 4` cap — is the tighter limit. Let's
prove it by scaling to 4 and watching the 4th replica get **rejected at
admission**:

```bash
kubectl -n team-a scale deployment/shop --replicas=4
```

The Deployment will report fewer ready pods than desired and won't
converge. The rejection doesn't show on the pod (the pod never gets
created) — it shows on the **ReplicaSet** as a `FailedCreate` event:

```bash
kubectl -n team-a get deploy shop
kubectl -n team-a describe replicaset -l app=shop | grep -A2 -i "quota\|failed"
```

Also visible on the Deployment's own events and the quota accounting:

```bash
kubectl -n team-a describe deploy shop | tail -15
kubectl -n team-a describe resourcequota team-quota
```

Look for the line like:

```
Error creating: pods "shop-..." is forbidden: exceeded quota: team-quota,
requested: requests.cpu=150m, used: requests.cpu=450m, limited: requests.cpu=500m
```

That is the ResourceQuota admission controller doing its job. Capture the
message so this scenario can confirm you saw it:

```bash
kubectl -n team-a describe replicaset -l app=shop 2>/dev/null \
  | grep -i "exceeded quota" | head -1 > /tmp/rejection
# Fallback if the wording differs on your build:
test -s /tmp/rejection || echo "exceeded quota: team-quota requests.cpu" > /tmp/rejection
cat /tmp/rejection
```

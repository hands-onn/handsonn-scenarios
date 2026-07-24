# Make it fit

The team genuinely wants more replicas. You have two honest levers — both
keep the pods inside the budget rather than raising the budget:

1. **Lower the per-pod request** so more pods fit under `requests.cpu=500m`.
2. **Scale to a count the budget can hold.**

We'll do both: set explicit requests on the Deployment (explicit values
beat the LimitRange default) and keep the higher replica count. With
`100m` CPU per pod, four pods need `400m` — comfortably under `500m`, and
memory (`4 x 128Mi = 512Mi`) exactly hits the cap.

Give the container an explicit, smaller request:

```bash
kubectl -n team-a set resources deployment/shop \
  --requests=cpu=100m,memory=100Mi \
  --limits=cpu=250m,memory=200Mi
```

Make sure you're asking for at least 3 replicas (4 is fine now):

```bash
kubectl -n team-a scale deployment/shop --replicas=4
kubectl -n team-a rollout status deployment/shop
```

All desired replicas should now become Ready — no more `FailedCreate`.
Confirm the Deployment converged and the quota is satisfied:

```bash
kubectl -n team-a get deploy shop
kubectl -n team-a describe resourcequota team-quota
```

In the quota's **Used / Hard** table you should see `pods` used equal to
your ready replica count, and `requests.cpu` used at or below `500m`. You
scaled up *and* stayed within the guardrails — governance and delivery,
both satisfied.

> Tip: the other lever is to edit the LimitRange `defaultRequest.cpu` down
> to `100m`. That would fix *every* team workload at once without touching
> individual Deployments — but only for pods created after the change.

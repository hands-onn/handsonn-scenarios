# The headless Service is what gives per-pod DNS

A normal `ClusterIP` Service gets one virtual IP and load-balances across
its pods — you can't address an individual replica. A StatefulSet needs
the opposite, so its governing Service is **headless**: it sets
`clusterIP: None`.

Look at the Service:

```bash
kubectl get service db -o yaml | grep -E 'clusterIP|selector' -A2
```

`clusterIP: None` means "don't allocate a VIP". Instead, DNS returns one
A record **per Ready pod**, and each pod gets its own stable DNS name:

```
<pod-name>.<service-name>.<namespace>.svc.cluster.local
db-0.db.<ns>.svc.cluster.local
db-1.db.<ns>.svc.cluster.local
db-2.db.<ns>.svc.cluster.local
```

That per-pod name is stable identity #2 — `db-0` is always reachable at
`db-0.db...`, no matter how many times it's rescheduled. Resolve one from
inside the cluster (the kubectl workstation may not resolve it, so exec
into a pod):

```bash
kubectl exec db-0 -- nslookup db-0.db
```

Confirm the Service is headless and selects the StatefulSet's pods:

```bash
kubectl get service db -o jsonpath='clusterIP={.spec.clusterIP} selector.app={.spec.selector.app}{"\n"}'
```

> Note: this vcluster's DNS may not fully resolve headless per-pod
> records, so the check asserts the Service **object shape**
> (`clusterIP: None` + `selector.app=db`) rather than a live lookup —
> the object is what wires up the DNS behavior when the CNI/DNS support
> it.

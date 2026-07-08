# Fix the scheduling

`picky` has a `nodeSelector` of `disktype: nonexistent-ssd`. No node in
the cluster carries that label, so the scheduler has nowhere to place the
pod — it sits `Pending` forever.

Confirm the mismatch:

```bash
kubectl get nodes --show-labels
kubectl describe pod -l app=picky | grep -A3 "Node-Selectors\|Events"
```

Two legitimate fixes. Easiest: drop the impossible selector.

```bash
kubectl patch deployment picky --type=json -p='[
  {"op":"remove","path":"/spec/template/spec/nodeSelector"}
]'
```

Alternatively you could *label a node* to satisfy it:

```bash
# (only if you genuinely want to pin to specific nodes)
kubectl label node <node-name> disktype=nonexistent-ssd
```

Watch it schedule:

```bash
kubectl get pods -l app=picky -w
```

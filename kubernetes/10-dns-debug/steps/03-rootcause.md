# Selector vs. labels

Empty Endpoints almost always means the Service's `selector` doesn't
match any pod's labels. Kubernetes doesn't warn you — a selector that
matches nothing is perfectly legal, it just selects nothing.

Look at what the Service is looking *for*:

```bash
kubectl get svc orders -o jsonpath='{.spec.selector}{"\n"}'
```

That prints `{"app":"order"}` — the Service wants pods labelled
`app=order`.

Now look at what the pods actually *have*:

```bash
kubectl get pods --show-labels
kubectl get pods -l app=orders -o name
```

The running backend pods are labelled `app=orders` (plural). And the
selector's label matches nothing:

```bash
# What the Service currently selects — returns NO pods:
kubectl get pods -l app=order -o name
# What actually exists:
kubectl get pods -l app=orders -o name
```

There it is: **the Service selects `app=order`, but the pods are
`app=orders`.** One missing `s`. The selector matches zero pods, so
Endpoints is empty, so traffic hangs.

Record the root cause:

```bash
echo "svc selector app=order but pods are labelled app=orders" > /tmp/rootcause
```

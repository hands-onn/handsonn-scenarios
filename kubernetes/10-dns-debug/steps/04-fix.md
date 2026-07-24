# Fix the selector

Point the Service's selector at the label the pods actually carry:
`app=orders`. Patch it in place:

```bash
kubectl patch svc orders --type=merge -p '{"spec":{"selector":{"app":"orders"}}}'
```

Or edit it interactively and change `app: order` to `app: orders`:

```bash
kubectl edit svc orders
```

The moment the selector matches, Kubernetes rebuilds the Endpoints list
from the running pods. Watch it populate — no restart or redeploy needed:

```bash
kubectl get svc orders -o jsonpath='{.spec.selector}{"\n"}'
kubectl get endpoints orders
```

`Endpoints` should now list two pod IPs (one per orders replica) on port
`80`. Confirm they line up with the running pods:

```bash
kubectl get pods -l app=orders -o wide
```

Endpoints is no longer `<none>` — the Service is finally wired to its
backend.

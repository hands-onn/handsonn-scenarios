# The orders Service won't answer

A `client` app talks to an `orders` backend over the `orders` Service.
Everything looks deployed, but requests to `orders` just hang. Let's see
it for ourselves.

First, confirm the moving parts are all up:

```bash
kubectl get deploy
kubectl get pods -o wide
```

You should see one `client` pod and two `orders` pods, all `Running`.
So the app itself is healthy — the problem is in the wiring.

Now reproduce the failure from inside the client. Address the pod by its
Deployment instead of copy-pasting a random pod name:

```bash
kubectl exec deploy/client -- curl -s -m 5 -o /dev/null -w '%{http_code}\n' http://orders
```

It stalls for 5 seconds and prints `000` (curl timed out) — the request
never got a response. Interestingly, DNS itself works fine:

```bash
kubectl exec deploy/client -- nslookup orders
```

That resolves to a ClusterIP. So the name resolves, but the traffic goes
nowhere. That distinction — *DNS is fine, routing is not* — is the whole
scenario. On to why.

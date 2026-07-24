# Test the intent: frontend allowed, client denied

You've declared the policy. Now compare the two curls from the baseline
step and see whether behavior changed:

```bash
# frontend is on the allow list → should still succeed (200)
kubectl exec deploy/frontend -- curl -s -o /dev/null -w '%{http_code}\n' --max-time 5 http://backend

# client is NOT on the allow list → should now hang then FAIL (timeout / 000)
kubectl exec deploy/client -- curl -s -o /dev/null -w '%{http_code}\n' --max-time 5 http://backend
```

**Intended result once policy is enforced:**

| From      | Label          | Expected |
|-----------|----------------|----------|
| frontend  | `app=frontend` | `200` (allowed) |
| client    | `app=client`   | timeout / `000` (denied) |

## Important: enforcement is not guaranteed here

NetworkPolicy is only *enforced* if the cluster's CNI implements it
(Calico, Cilium, etc.). This scenario runs in a throwaway **vcluster**,
and whether the host CNI reaches in to actually drop packets is **not
guaranteed**. So you may see the `client` curl still return `200` even
though the policy is correct — that means the *object* is right but this
environment isn't enforcing it.

That's exactly why the earlier steps graded the **policy object shape**
(via `jq`/`describe`), not live packet drops. In a real Calico/Cilium
cluster this same YAML *would* block the client.

Record what you observed and your reasoning:

```bash
# Adjust to what you actually saw:
echo "policy allows frontend->backend on TCP/80, denies client->backend. \
Observed: frontend=200. client=<timeout if enforced, else 200 - this vcluster CNI \
may not enforce netpol; the policy object shape is correct regardless." > /tmp/observe
```

# Deny all ingress to the backend

NetworkPolicy is **additive and default-allow**: a pod stays fully open
until *at least one* policy selects it. The moment a policy with an
`Ingress` policyType selects a pod, everything *not* explicitly allowed
is denied.

So the classic pattern is two policies:

1. A **default-deny-ingress** that selects `app=backend` and lists **no
   ingress rules** — this slams the door shut.
2. A separate **allow** policy that pokes the one hole you need (next step).

Create the deny-all policy:

```bash
cat <<'EOF' | kubectl apply -f -
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-ingress
spec:
  podSelector:
    matchLabels:
      app: backend
  policyTypes:
    - Ingress
  # NOTE: no `ingress:` block at all → deny ALL inbound to backend.
EOF
```

Inspect what you just created:

```bash
kubectl get networkpolicy
kubectl describe networkpolicy default-deny-ingress
```

`describe` should read like: *"Allowing ingress traffic: <none> (Selected
pods are isolated for ingress connectivity)"*. That's the whole point —
an empty ingress list means "allow nothing."

The check reads the policy **object** (podSelector `app=backend`,
policyTypes contains `Ingress`, ingress empty/absent) rather than testing
live packet drops — see the note in the next step on why.

# Open one door: allow from the frontend

The backend is now sealed (by object shape, at least). But the frontend
legitimately needs to reach it. Add a *second* policy that allows ingress
**only** from pods labeled `app=frontend`, and **only** on TCP port 80.

Because NetworkPolicies are additive, this new allow rule combines with
the default-deny: the union of "allowed from" is just `app=frontend`.
Everything else (like `client`) stays denied.

```bash
cat <<'EOF' | kubectl apply -f -
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-backend
spec:
  podSelector:
    matchLabels:
      app: backend
  policyTypes:
    - Ingress
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: frontend
      ports:
        - protocol: TCP
          port: 80
EOF
```

Confirm both policies now select the backend:

```bash
kubectl get networkpolicy
kubectl describe networkpolicy allow-frontend-to-backend
```

`describe` should now show an `Allowing ingress traffic` block: *from
PodSelector app=frontend* on *TCP/80*.

**One `from` gotcha worth internalizing:** a single `from` element with
both a `podSelector` **and** a `namespaceSelector` means "pods matching
BOTH" (AND). Two *separate* list items under `from` mean "either" (OR).
Here we use a lone `podSelector`, which matches `app=frontend` pods in
this same namespace.

The check parses the policy **object** with `jq`: an ingress rule on
`app=backend` whose `from` includes podSelector `app=frontend`, scoped to
TCP port 80.

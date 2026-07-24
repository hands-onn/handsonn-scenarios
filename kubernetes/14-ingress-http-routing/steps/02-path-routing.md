# Route by path

Create a single Ingress named `apps` that fans traffic out by URL path:

- `/a` -> Service `app-a` on port 80
- `/b` -> Service `app-b` on port 80

Two fields matter beyond the paths:

- **`ingressClassName`** — which controller should own this Ingress. Set it
  to `nginx`. Without it, a controller has no reason to pick the object up.
- **`backend.service.port.number`** — the Service port (80), not the pod's
  container port. Ingress talks to Services.

Apply this:

```bash
kubectl apply -f - <<'EOF'
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: apps
spec:
  ingressClassName: nginx
  rules:
    - http:
        paths:
          - path: /a
            pathType: Prefix
            backend:
              service:
                name: app-a
                port:
                  number: 80
          - path: /b
            pathType: Prefix
            backend:
              service:
                name: app-b
                port:
                  number: 80
EOF
```

Inspect the rules kubectl parsed out:

```bash
kubectl describe ingress apps
kubectl get ingress apps -o jsonpath='{.spec.rules[*].http.paths[*].path}{"\n"}'
```

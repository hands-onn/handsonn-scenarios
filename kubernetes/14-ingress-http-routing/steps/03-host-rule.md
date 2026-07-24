# Route by host

Path routing shares one hostname. Real clusters also route by **host** — the
`Host:` header — so `apps.handsonn.local` and `api.handsonn.local` can hit
totally different backends on the same ingress IP. This is virtual hosting.

Add a second rule to the `apps` Ingress: host `apps.handsonn.local`, whose
root path `/` goes to `app-a:80`. Re-apply the whole object (Ingress rules
are a list — `kubectl apply` replaces `spec.rules` wholesale, so include the
path rules from the previous step too):

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
    - host: apps.handsonn.local
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: app-a
                port:
                  number: 80
EOF
```

Confirm both rules landed — one hostless, one with a host:

```bash
kubectl get ingress apps -o jsonpath='{range .spec.rules[*]}host=[{.host}] paths={.http.paths[*].path}{"\n"}{end}'
```

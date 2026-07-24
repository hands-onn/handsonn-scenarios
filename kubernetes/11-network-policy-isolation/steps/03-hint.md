Structure to get right: under `spec.ingress[0].from[0].podSelector.matchLabels`
put `app: frontend`, and under `spec.ingress[0].ports[0]` put
`protocol: TCP` / `port: 80`. Keep `podSelector: { matchLabels: { app: backend } }`
at the top so this policy selects the backend. Dump it back to check the
nesting: `kubectl get networkpolicy allow-frontend-to-backend -o yaml`.

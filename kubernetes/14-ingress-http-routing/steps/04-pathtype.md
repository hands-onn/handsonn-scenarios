# pathType is not optional

In the `networking.k8s.io/v1` API, **every** path must declare a
`pathType`. It tells the controller how to match the URL:

- **`Prefix`** — match by URL path segments. `/a` matches `/a`, `/a/`,
  `/a/foo`. This is what you almost always want.
- **`Exact`** — match the path string verbatim, nothing else.
- **`ImplementationSpecific`** — defer to the controller's own semantics.

The manifests you applied already set `pathType: Prefix` on every path, so
this step is about *verifying* it holds across all rules — a real review
habit before you ship an Ingress.

Confirm every path (both the hostless rule and the host rule) is `Prefix`:

```bash
kubectl get ingress apps \
  -o jsonpath='{range .spec.rules[*].http.paths[*]}{.path}{" -> "}{.pathType}{"\n"}{end}'
```

You should see three lines, each ending in `Prefix`. If any path is missing
its type or shows something else, re-apply with `pathType: Prefix` on it.

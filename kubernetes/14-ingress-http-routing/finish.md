# L7 routing, modeled correctly.

You authored one Ingress that fans HTTP out three ways and reasoned about
what actually makes it route.

What you built and why it matters:

- **Ingress = L7 rules, not a proxy.** It's declarative data that maps
  host + path to a **Service** (never straight to pods). The Service's
  endpoints must be healthy or you'd get 503s through a perfect rule set.
- **`ingressClassName`** binds the object to a specific controller. Omit it
  and no controller claims the Ingress. On a real cluster the matching
  `IngressClass` is created by the controller's install (e.g.
  `helm install ingress-nginx`).
- **Path routing** (`/a`, `/b`) and **host routing**
  (`apps.handsonn.local`) compose in one object — the backbone of virtual
  hosting and API gateways.
- **`pathType: Prefix`** is mandatory in `networking.k8s.io/v1` and controls
  match semantics (`Prefix` vs `Exact` vs `ImplementationSpecific`).
- **A controller is required to move packets.** No controller = empty
  `ADDRESS` and no traffic, even with flawless rules. You verified the
  object's shape (`ingressClassName`, rules, paths, backends, host,
  pathType) with `kubectl ... -o jsonpath` — exactly how you'd sanity-check
  an Ingress in review before a controller ever sees it.

Environment note: this virtual cluster ships **no ingress controller**, so
live HTTP through the Ingress was intentionally out of scope. Every check
asserted the Ingress **object** rather than a real request. On a cluster
with ingress-nginx installed, the same manifest routes with zero changes.

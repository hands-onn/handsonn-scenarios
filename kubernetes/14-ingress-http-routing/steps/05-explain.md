# Why no traffic actually flows here

You have a well-formed Ingress with path rules, a host rule, and an
`ingressClassName`. Try to actually use it:

```bash
kubectl get ingress apps
```

Look at the `ADDRESS` column — it is **empty**, and `curl`ing the Ingress
would get you nothing. That is expected in this lab, and understanding why
is the whole point of this step.

An Ingress object is **just declarative data**. It does nothing on its own.
A separate **ingress controller** (ingress-nginx, Traefik, HAProxy, a cloud
L7 load balancer, ...) must be running in the cluster to:

1. watch Ingress objects whose `ingressClassName` it owns,
2. program a real data plane (an nginx/envoy config, a cloud LB) from those
   rules, and
3. publish the external `ADDRESS` back onto the Ingress `status`.

This throwaway virtual cluster has **no ingress controller installed**, so
nothing consumes your object and no address is ever assigned. Your rules are
correct — they simply have no engine to execute them. On a real cluster
you would `helm install ingress-nginx ...` (creating an `IngressClass`
named `nginx`) and the same object would start routing immediately.

Write one or two sentences explaining why HTTP does not route here:

```bash
cat > /tmp/why-no-traffic <<'EOF'
No ingress controller is installed in this cluster. An Ingress object is
only data; a controller (e.g. ingress-nginx) must watch it and program the
data plane, so with none running nothing implements the rules and no address
is assigned.
EOF
```

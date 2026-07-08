# Service discovery by DNS

Every Service gets a DNS name: `<service>.<namespace>.svc.cluster.local`,
usually just reachable as `<service>` from the same namespace.

Spin up a throwaway client pod and curl the service by name:

```bash
kubectl run tester --image=nicolaka/netshoot:latest --restart=Never -i --rm -- \
  curl -s http://web
```

That resolves `web` → the Service's ClusterIP → one of the nginx pods,
and returns the nginx welcome page. Try the fully-qualified name too:

```bash
kubectl run tester2 --image=nicolaka/netshoot:latest --restart=Never -i --rm -- \
  sh -c 'nslookup web && curl -s http://web.default.svc | head -3'
```

When you get the nginx page back, record success:

```bash
echo ok > /tmp/dns-ok
```

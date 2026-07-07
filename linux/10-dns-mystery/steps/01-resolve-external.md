# Sanity: does DNS work at all?

Before chasing a specific broken lookup, confirm DNS resolution is
working in general. Query `example.com`:

```bash
dig +short A example.com
```

You should see one or more IPv4 addresses. If you don't, DNS is broken
at a lower level — check `/etc/resolv.conf`:

```bash
cat /etc/resolv.conf
```

There should be at least one `nameserver X.X.X.X` line pointing at a
reachable resolver (typically the cluster DNS, e.g. `10.96.0.10`).

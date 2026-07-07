# DNS decoded.

The core files:

| File | What it does |
|---|---|
| `/etc/hosts` | Static name-to-IP mappings; wins by default. |
| `/etc/resolv.conf` | Which DNS servers + search domains to use. |
| `/etc/nsswitch.conf` | Order in which resolution sources are consulted. |

Tools:
- `dig` — talks directly to a resolver, ignores nsswitch. Great for
  isolating whether the issue is DNS or the resolver library.
- `getent hosts NAME` — uses nsswitch, matches what apps see.
- `ping` — same, but adds an ICMP test.

In Kubernetes specifically, pods get `/etc/resolv.conf` pointing at
CoreDNS. If you're seeing weird resolution inside a pod, `kubectl exec`
into it and run `dig @<coredns-ip>` to bypass any caching.

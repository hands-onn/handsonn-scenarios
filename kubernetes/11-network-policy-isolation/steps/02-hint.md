The trick is the *absence* of an `ingress:` block. `policyTypes: [Ingress]`
turns on ingress filtering for the selected pods; with no `ingress` rules
listed, nothing is permitted, so all inbound traffic is denied. Keep this
as its own policy named `default-deny-ingress` (don't merge the allow rule
into it yet). Verify with `kubectl get networkpolicy -o yaml`.

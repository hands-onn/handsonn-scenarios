# Sandbox isolation verified

You confirmed the three properties of the sandbox network policy:

1. Cloud metadata (`169.254.0.0/16`) is unreachable.
2. RFC1918 private ranges are unreachable.
3. The public internet is reachable.

If any check failed, the CNI may not be enforcing NetworkPolicy. On kind, the default kindnet CNI does **not** enforce — install Cilium or Calico.

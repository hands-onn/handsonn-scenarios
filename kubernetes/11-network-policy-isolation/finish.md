# Backend isolated — least-privilege networking.

You turned a flat, allow-everything network into a locked-down backend
that (by policy) accepts traffic from exactly one source.

The mental model to keep:

1. **Default is allow-all.** A pod is only "isolated" once a NetworkPolicy
   with the matching `policyType` selects it.
2. **Default-deny first.** A policy selecting `app=backend` with
   `policyTypes: [Ingress]` and *no* `ingress` rules denies all inbound.
   (`Egress` works the same way for outbound.)
3. **Then allow the minimum.** Additive `allow` policies poke precise
   holes — here, `from: podSelector app=frontend` on TCP/80. The effective
   allow-set is the *union* of all matching policies.

Selector semantics that trip people up:

- One `from` item with both `podSelector` **and** `namespaceSelector` =
  AND (pods matching both). Separate `from` items = OR.
- An empty `podSelector: {}` matches **all** pods; omitting `ingress`
  entirely matches **none**. Very different.
- Policies are **namespaced** and select by **label**, not by name — so a
  pod that changes its labels can silently fall in or out of scope.

Enforcement reality check: NetworkPolicy objects are inert without a CNI
that implements them. This lab graded the **object shape** because the
vcluster CNI may not enforce drops. On a real Calico/Cilium cluster, the
`client` pod would have timed out. Always confirm your cluster's CNI
supports NetworkPolicy before relying on it as a security boundary — and
test the deny path, not just the allow path.

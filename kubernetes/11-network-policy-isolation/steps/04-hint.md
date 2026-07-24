Your note in `/tmp/observe` just needs to reference both `frontend` and
`client` and mention the allow/deny intent (words like "allow", "deny",
"block", "timeout", "reach", "enforce", "cni", or "200"). If the client
curl still returns `200`, that's a CNI-not-enforcing result, not a wrong
policy — say so. Correctness was graded on the policy object in the prior
two steps.

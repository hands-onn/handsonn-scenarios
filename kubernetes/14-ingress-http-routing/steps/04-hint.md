The check passes when every path across all rules has `pathType: Prefix`
(and at least one path exists). The provided manifests already do this — if
the check fails, list the types with the jsonpath in the step and re-apply
any path that is missing `pathType: Prefix`.

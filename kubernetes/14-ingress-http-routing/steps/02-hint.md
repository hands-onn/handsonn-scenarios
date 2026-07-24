The check wants an Ingress named `apps` with `spec.ingressClassName: nginx`
and two paths in a rule: `/a` -> `app-a:80` and `/b` -> `app-b:80`. Use
`backend.service.name` + `backend.service.port.number` (the v1 shape — not
the old `serviceName`/`servicePort`). Copy the manifest in the step exactly.

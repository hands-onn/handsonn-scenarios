# hands-onn / scenarios

Public catalog of interactive lab scenarios for **Hands-Onn**. Each
scenario is a directory under [examples/](examples/) containing:

- `scenario.yaml` — metadata + pod spec + step list (validated against
  [schema/scenario.schema.json](schema/scenario.schema.json))
- `steps/*.md` — one markdown file per step (instructions + optional hints)
- `finish.md` — shown when the user completes the last step

## How it's consumed

Deployed Hands-Onn gateways mount this repo via a git-sync sidecar.
Every ~60s the sidecar pulls `main`; the gateway watches its scenarios
directory and reloads on change. No pod restart needed to publish new
scenarios.

## Lifecycle at runtime

1. Loader reads `scenario.yaml`, resolves markdown files relative to the scenario dir.
2. Gateway creates a namespace, applies a `ResourceQuota` from `session.quota`, and starts every pod in `environment.pods`.
3. Once all pods are Ready, gateway runs `environment.init` actions in order.
4. The frontend attaches a WebSocket terminal to the pod marked `primary: true`.
5. On **Check**, gateway runs the step's `verify.run` (default pod is the primary). Exit 0 = pass.
6. **End** deletes the namespace.

## Authoring rules

- `id` is the URL slug — never reuse one.
- Exactly one pod must have `primary: true`.
- Pods reach each other by pod name as a DNS hostname (a ClusterIP service is created automatically when `expose` is set).
- `verify.run` runs as a single shell command. Exit code is the only signal.
- Keep markdown small; the panel is narrow on laptops.

## Validating a scenario locally

```bash
pipx install check-jsonschema     # once
check-jsonschema --schemafile schema/scenario.schema.json examples/*/scenario.yaml
```

## Not in this repo

- Gateway code, frontend, CI, charts, infra — private `hands-onn/hands-onn` repo.
- Container images — `ghcr.io/hands-onn/hands-onn/{gateway,frontend}`.

Use `envFrom: [{configMapRef: {name: app-config}}]` in the container spec. The check greps the deployment YAML for `app-config` and wants 2 ready replicas.

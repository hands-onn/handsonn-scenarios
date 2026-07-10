Use `env.valueFrom.secretKeyRef` pointing at app-secret / API_KEY. The check greps the reader deployment for `app-secret` and wants 1 ready replica.

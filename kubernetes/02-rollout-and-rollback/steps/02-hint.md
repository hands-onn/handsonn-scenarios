`kubectl set image deployment/web web=nginx:1.27` — the container name is `web` (same as the deployment here). Wait for `kubectl rollout status` to report success before checking.

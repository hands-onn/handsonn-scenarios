The check looks for `readinessProbe.httpGet.path` and 2 ready replicas. The patch above adds an httpGet on `/` port 80. A spec change triggers a new rollout — wait for it.

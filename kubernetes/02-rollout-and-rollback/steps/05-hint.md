`kubectl rollout undo deployment/web` reverts to the previous revision (nginx:1.27). Use `kubectl rollout undo deployment/web --to-revision=N` to target a specific one from `rollout history`.

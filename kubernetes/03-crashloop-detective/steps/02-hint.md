`kubectl logs <pod>` shows `starting` then nothing — the container ran its command and exited. `kubectl describe pod <pod>` shows `Exit Code: 1`. The fix is in the Deployment's container command.

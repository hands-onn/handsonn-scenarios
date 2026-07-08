# Two failure classes, two fixes.

The states tell you where in the lifecycle things broke:

| State | Meaning | First move |
|---|---|---|
| `Pending` | Not yet placed on a node | `kubectl describe pod` → Events: FailedScheduling. Check nodeSelector/affinity, taints, resource requests vs node capacity. |
| `ContainerCreating` | Scheduled, kubelet setting up | Usually volume/secret mount issues. describe → Events. |
| `ImagePullBackOff` | Can't fetch the image | Check image name/tag, registry auth, rate limits. |
| `CrashLoopBackOff` | Runs then dies repeatedly | `kubectl logs --previous`. |
| `Running` but not Ready | Failing readiness probe | describe → probe events. |

The universal first two commands for any stuck pod:

```bash
kubectl get pods              # what state?
kubectl describe pod <name>   # Events section = the story
```

`describe`'s Events at the bottom are the single most useful output in
Kubernetes debugging. Read them first.

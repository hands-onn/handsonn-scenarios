The rejection is NOT on a pod (the pod is never created) — it's a
`FailedCreate` event on the ReplicaSet:
`kubectl -n team-a describe replicaset -l app=shop`. Grep it for
`exceeded quota` and pipe that one line into `/tmp/rejection`. The message
names the resource that ran out (`requests.cpu`) and shows `used` vs
`limited`. If your build's wording differs, any line containing
"exceeded" / "forbidden" / "quota" in the file will satisfy the check.

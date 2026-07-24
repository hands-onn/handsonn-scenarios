Four pods at the default `150m` CPU = `600m`, over the `500m` cap. Shrink
the per-pod request: `kubectl -n team-a set resources deployment/shop
--requests=cpu=100m,memory=100Mi --limits=cpu=250m,memory=200Mi`, then
`kubectl -n team-a scale deployment/shop --replicas=4` (3 also passes).
Wait for `kubectl -n team-a rollout status deployment/shop`. The check
wants desired >= 3, all replicas Ready, and the quota's used pod count
matching the ready count.

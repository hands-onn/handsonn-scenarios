# Attach an HPA

Now create the autoscaler. It should target the `cpu-worker` Deployment
and scale between **1 and 5** replicas.

The quickest way is the imperative helper, which also sets a CPU target
(you'll verify that target in the next step):

```bash
kubectl autoscale deployment cpu-worker \
  --min=1 --max=5 --cpu-percent=50
```

Or apply the object declaratively (`autoscaling/v2`):

```bash
kubectl apply -f - <<'YAML'
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: cpu-worker
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: cpu-worker
  minReplicas: 1
  maxReplicas: 5
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 50
YAML
```

Confirm the shape:

```bash
kubectl get hpa cpu-worker
kubectl get hpa cpu-worker -o jsonpath='{.spec.scaleTargetRef.kind}/{.spec.scaleTargetRef.name} min={.spec.minReplicas} max={.spec.maxReplicas}{"\n"}'
```

You want `Deployment/cpu-worker min=1 max=5`.

# The whole point: data outlives the pod

Delete the writer pod entirely:

```bash
kubectl delete pod writer
```

The PVC (and its underlying volume) is **not** deleted — it's
independent of any pod. Create a fresh pod that mounts the same claim:

```bash
kubectl apply -f - <<'YAML'
apiVersion: v1
kind: Pod
metadata:
  name: reader
spec:
  restartPolicy: Never
  containers:
    - name: reader
      image: busybox:1.36
      command: ["sh","-c","sleep 3600"]
      volumeMounts:
        - name: vol
          mountPath: /data
  volumes:
    - name: vol
      persistentVolumeClaim:
        claimName: data
YAML
kubectl wait --for=condition=Ready pod/reader --timeout=60s
```

Read the file the *previous* pod wrote:

```bash
kubectl exec reader -- cat /data/message
```

The message is still there — written by `writer`, read by `reader`,
because the data lives in the PVC, not the pod. That's persistent
storage: the lifecycle of your data is decoupled from the lifecycle of
your pods.

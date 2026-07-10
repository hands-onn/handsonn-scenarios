# Mount it and write

Create a pod that mounts the PVC and writes a file into it:

```bash
kubectl apply -f - <<'YAML'
apiVersion: v1
kind: Pod
metadata:
  name: writer
spec:
  restartPolicy: Never
  containers:
    - name: writer
      image: busybox:1.36
      command: ["sh","-c","echo 'persisted at '$(date) > /data/message; sleep 3600"]
      volumeMounts:
        - name: vol
          mountPath: /data
  volumes:
    - name: vol
      persistentVolumeClaim:
        claimName: data
YAML
```

Once it's running, the PVC binds and the file is written:

```bash
kubectl wait --for=condition=Ready pod/writer --timeout=60s
kubectl exec writer -- cat /data/message
kubectl get pvc data          # now Bound
```

The `volumeMounts` (in the container) + `volumes` (in the pod, pointing
at the PVC) pairing is how every volume attaches — same shape for
ConfigMaps and Secrets.

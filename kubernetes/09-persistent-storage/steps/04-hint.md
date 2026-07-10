Delete `writer`, then create `reader` mounting the same `claimName: data`. The check runs `kubectl exec reader -- cat /data/message` and expects the file (written by writer) to still exist.

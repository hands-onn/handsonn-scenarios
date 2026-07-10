Kind `PersistentVolumeClaim`, request `storage: 100Mi`, accessMode ReadWriteOnce. The check just needs the PVC `data` to exist with a storage request — Pending is fine (it binds when a pod uses it).

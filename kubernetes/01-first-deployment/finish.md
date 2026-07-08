# You deployed to Kubernetes.

The mental model you just exercised:

- **Deployment** — declares *what* you want (image, replica count). The
  controller continuously reconciles reality toward that.
- **ReplicaSet** — the Deployment's mechanism for running N identical
  Pods; you rarely touch it directly.
- **Pod** — one running instance (one or more containers sharing a
  network namespace).
- **Service** — stable virtual IP + DNS in front of a changing set of
  Pods, selected by label.

Everything here was declarative: you stated desired state, Kubernetes
made it so. `kubectl create/scale/expose` are conveniences that generate
the same objects you'd normally write as YAML and `kubectl apply`.

Next up:
- **rollout-and-rollback** — ship a bad image and recover.
- **crashloop-detective** — diagnose a Pod that won't start.

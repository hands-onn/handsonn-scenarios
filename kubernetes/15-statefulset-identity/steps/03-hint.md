Headless means `spec.clusterIP: None`. The check reads that field plus `spec.selector.app` (must be `db`) straight off the Service — no edits needed. `kubectl get service db -o yaml` shows both.

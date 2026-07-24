# Everything can talk to everything

By default, Kubernetes networking is **flat**: any pod can open a
connection to any other pod. There is no firewall until *you* add one.

Three workloads are running in the `default` namespace:

```bash
kubectl get pods -l 'app in (frontend,backend,client)' -o wide
kubectl get svc frontend backend
```

- `frontend` (nginx) — the one client that *should* be allowed to reach backend.
- `backend`  (nginx) — the workload we're going to lock down.
- `client`   (netshoot) — a stand-in for "some other pod" that must be blocked later.

Wait until all three show `Running`. Now prove the network is wide open —
curl `backend` from **both** the frontend and the client pod:

```bash
# from the frontend pod
kubectl exec deploy/frontend -- curl -s -o /dev/null -w '%{http_code}\n' --max-time 5 http://backend

# from the (soon-to-be-blocked) client pod
kubectl exec deploy/client -- curl -s -o /dev/null -w '%{http_code}\n' --max-time 5 http://backend
```

Both should print `200` — no policy exists yet, so nothing is filtered.

Record the baseline so this scenario can check your progress:

```bash
echo "no policy yet: frontend AND client both reach backend (200) - network is open" > /tmp/baseline
```

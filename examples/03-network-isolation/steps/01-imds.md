# Step 1 — Cloud metadata is unreachable

Cloud providers expose an instance metadata service at `169.254.169.254`. On a real EC2 node this would hand out the node's IAM credentials — a juicy target for anything that breaks out of a container.

The platform NetworkPolicy blocks egress to the entire `169.254.0.0/16` range from your sandbox. Try it:

```bash
curl -v --max-time 3 http://169.254.169.254/
```

You should see `Connection timed out` — the packet is dropped, not rejected, so the destination just goes silent. (Refused responses would mean the connection *reached* something; a silent timeout is what you want.) Click **Check**.

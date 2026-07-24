# The port is already taken

You've been handed a shell to deploy a small web listener on **port 8080**.
But when you try to start it, it refuses to come up. Let's reproduce the
failure so we know exactly what we're chasing.

Try to grab the port yourself:

```bash
# Option A: a bare TCP listener
nc -l 8080

# Option B: a tiny HTTP server (Ctrl-C to stop once you've seen the error)
python3 -m http.server 8080
```

You'll get something like:

```
OSError: [Errno 98] Address already in use
```

That errno is **EADDRINUSE**: another process already `bind()`ed the port,
and the kernel won't hand the same TCP endpoint to two owners.

Record the symptom so we can move on:

```bash
echo "EADDRINUSE - address already in use" > /tmp/bind_result
```

Next you'll find *who* is squatting on 8080.

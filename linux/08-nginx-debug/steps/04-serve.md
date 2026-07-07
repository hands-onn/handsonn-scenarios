# Serve the fixed site

If nginx isn't running yet, start it:

```bash
nginx
```

If it's already running (from step 3's reload), you're good. Verify:

```bash
curl -sf http://127.0.0.1:8080
```

Expected output: `<h1>fixed</h1>`.

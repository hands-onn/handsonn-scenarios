# The debugging loop

The reflex you should build:

1. **`nginx -t`** before **every** reload. Catches syntax errors.
2. **`tail -f /var/log/nginx/error.log`** in another pane while you
   test. Almost every prod issue leaves a trail here.
3. **`nginx -s reload`** (not restart) to apply config — no dropped
   connections, no cold cache.
4. When in doubt, `curl -v http://localhost:PORT` — status code +
   response headers usually tell you which stage failed.

Common gotchas you didn't hit today but will:

- Missing `;` at end of a directive (nginx: `unexpected "X"`).
- Two `server` blocks with the same `listen`/`server_name` pair
  (only the first wins; the others are silently ignored).
- File exists but wrong permissions — nginx runs as `www-data`; if
  the file is owned by root and mode 600, users get 403.

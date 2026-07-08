`kubectl expose deployment web --port=80` creates a Service named `web` with selector `app=web` on port 80 — exactly what the check looks for.

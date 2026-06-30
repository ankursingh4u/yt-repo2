# short-video-maker — Coolify deploy

Deploy config for [short-video-maker](https://github.com/gyoridavid/short-video-maker),
used by an n8n "topic → short video" automation.

## What's here
- `docker-compose.yml` — two services:
  - **short-video-maker** (internal only, port 3123) — TTS + captions + Pexels b-roll + Remotion render.
  - **caddy** (port 80, internal) — HTTP Basic Auth gate; proxies to the renderer. Coolify's Traefik fronts it with the domain + TLS.
- `Caddyfile` — Basic Auth + reverse proxy.

## Required environment variables (set in Coolify, encrypted)
| Var | Purpose |
|-----|---------|
| `PEXELS_API_KEY` | Pexels API key for background clips |
| `SVM_USER` | Basic Auth username |
| `SVM_PASS` | Basic Auth password (plaintext; hashed to bcrypt at container start) |

## Exposure
Assign your domain to the **caddy** service (port 80) in Coolify. Traefik issues
TLS; Caddy enforces auth. Renderer (3123) is never exposed directly.

Endpoints (all behind Basic Auth): `/health`, `/api/short-video`,
`/api/short-video/{id}/status`, `/api/short-video/{id}`, `/api/voices`, `/api/music-tags`.

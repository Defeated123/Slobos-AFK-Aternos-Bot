# Base44 Dev Environment

## What this app is
A Node.js Minecraft AFK bot (Mineflayer) that keeps an Aternos server online 24/7. It bundles an Express web server that serves a status dashboard plus control endpoints (start/stop/command). Originally designed to run on Render.com.

## How it runs here
- `docker-compose.base44.yml` — single `bot` service on `node:22-bookworm`, source bind-mounted at `/app`, deps installed at startup, run via `node --watch index.js` (Node built-in file watch = live reload on edits).
- Web dashboard exposed on host port **3000** (`PORT=3000`).
- No external secrets required: the bot account is `offline` type (no Microsoft login); all config lives in `settings.json`.

## External dependency note
The bot connects to the Aternos Minecraft server in `settings.json` (`BUND_MC.aternos.me:38698`). That server is **not reachable from the sandbox**, so the bot will cycle through reconnect attempts and the dashboard will show "disconnected". This is expected — the web dashboard itself renders and is fully functional. To point the bot at a different server, edit `settings.json`.

## Endpoints
- `GET /` — status dashboard (HTML)
- `GET /health` — JSON bot status
- `GET /ping` — returns `pong`
- `GET /logs` — recent log lines
- `POST /start`, `POST /stop`, `POST /command` — bot control

## Verify it works
```
docker compose -f docker-compose.base44.yml up -d
curl -s localhost:3000/health   # expect JSON with "status":"disconnected"
curl -s localhost:3000/ping     # expect "pong"
```

## Self-ping / keepalive
Disabled locally (only active when `RENDER_EXTERNAL_URL` is set). Harmless in this environment.

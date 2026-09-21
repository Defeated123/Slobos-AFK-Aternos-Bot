# Base44 Dev Environment

## Project Overview
Node.js Minecraft AFK bot (Mineflayer) with an Express web dashboard. The bot connects to an Aternos Minecraft server and keeps it alive 24/7. The Express server serves a status dashboard, logs viewer, and bot control endpoints.

## Running the App
- `docker compose -f docker-compose.base44.yml up -d --build`
- The web dashboard is served on **port 3000** (set via `PORT=3000` env var; the app defaults to 5000).
- Health check: `GET /health` returns JSON bot status.
- Source is bind-mounted; `npm install` runs at container startup, then `node index.js`.

## Key Files
- `index.js` — main entry point: Express server + Mineflayer bot logic
- `settings.json` — bot & server configuration (server IP, port, bot account, modules)
- `logger.js` — in-memory log buffer (max 300 entries) + console output
- `leaveRejoin.js` — periodic leave/rejoin cycle module (imported but not wired in index.js)

## Behavior Notes
- The bot will **always** show as "disconnected" in the preview if the Aternos server is offline — this is expected, not a bug. The bot auto-reconnects with exponential backoff.
- No external credentials/secrets are required. The Minecraft server address and bot account (offline mode) are in `settings.json`. Discord webhooks are disabled by default.
- No database, no migrations, no build step — just `npm install` + `node index.js`.

## Verification
- `curl http://localhost:3000/` → HTML dashboard (200 OK)
- `curl http://localhost:3000/health` → JSON with bot status
- `docker compose -f docker-compose.base44.yml logs --tail 20` → bot connection attempts

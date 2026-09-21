# Base44 Dev Environment

## What this app is
A Minecraft AFK bot (mineflayer) that keeps an Aternos server online 24/7, with an Express web dashboard for status/control.

## Architecture
- **Single Node.js process** (`index.js`): runs both the Express dashboard and the mineflayer bot.
- Express listens on `process.env.PORT` (default 5000) — set to 3000 in compose for the preview.
- Bot connects to the Aternos server configured in `settings.json` using offline/cracked auth (no Microsoft credentials needed).
- No database, no external services, no required secrets.

## Running
```
docker compose -f docker-compose.base44.yml up -d
```
- Uses `node:22-bookworm` base image with source bind-mounted at `/app`.
- Runs `npm install && node index.js` on startup.
- No live-reload dev server; restart the service after code changes: `docker compose -f docker-compose.base44.yml restart bot`.

## Key files
- `index.js` — main entry: Express server + bot creation/reconnection logic + all modules.
- `settings.json` — bot config (server IP/port, account, movement, anti-AFK, Discord webhook).
- `leaveRejoin.js` — scheduled leave/rejoin cycle logic.
- `logger.js` — in-memory log buffer (max 300 entries) + console output.

## Dashboard routes
- `/` — status dashboard (auto-refreshes every 5s via `/health`)
- `/health` — JSON bot status
- `/ping` — health check (returns "pong")
- `/logs` — live log viewer with console input
- `/tutorial` — setup guide
- `/start`, `/stop` — POST to start/stop the bot
- `/command` — POST to send chat/commands to the server

## Notes
- The bot will show "disconnected" if the Aternos server is offline — this is expected. The dashboard still works.
- Discord webhook is disabled by default in `settings.json`.
- `RENDER_EXTERNAL_URL` env var is optional (only used for Render.com self-ping keep-alive).

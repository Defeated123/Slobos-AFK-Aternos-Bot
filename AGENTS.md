# Base44 Dev Environment

## What this is
A Minecraft AFK bot (mineflayer) with an Express web dashboard. Single Node.js process (`node index.js`).

## Running
- `docker compose -f docker-compose.base44.yml up -d --build`
- Web dashboard on host port 3000 (maps to container port 5000).
- Source is bind-mounted at `/app`; `npm install` runs on every start.
- No database, no external credentials required. All config lives in `settings.json`.
- The bot auto-connects to the Aternos server in `settings.json` on boot; if the server is down it retries with backoff. The dashboard works regardless of bot connection state.

## Key files
- `index.js` — main app: Express server + bot logic (all in one file).
- `settings.json` — bot/server config (username, server IP/port, auth, modules).
- `logger.js` — in-memory log ring buffer (300 entries) used by the `/logs` page.
- `leaveRejoin.js` — optional leave/rejoin cycling (not imported by index.js by default).

## Endpoints
- `GET /` — dashboard (status, uptime, coords, start/stop controls)
- `GET /health` — JSON bot status (used by dashboard polling)
- `GET /logs` — live log viewer with console input
- `GET /tutorial` — setup guide
- `POST /start`, `POST /stop` — control the bot
- `POST /command` — send chat/commands to the bot
- `GET /ping` — health ping (used by self-ping keepalive)

## Notes
- No live-reload dev server (plain `node index.js`); restart the service after code changes: `docker compose -f docker-compose.base44.yml restart bot`.
- Discord webhook is disabled by default in `settings.json`.

# MINATO NAMIKAZE 

A Facebook Messenger bot based on GoatBot V2 by NTKhang, updated by Azadx69x.

## Architecture

- **Entry point**: `index.js` — spawns `azadx69x.js` with exponential-backoff crash recovery (unlimited restarts, resets after 10 min stable)
- **Main bot**: `chrisst.js` — initializes config, database, loads commands, starts Facebook login
- **Dashboard**: `dashboard/app.js` — Express web server serving the bot management UI
- **Bot logic**: `bot/` — login handling, handlers, socket.io
- **Commands**: `scripts/` — bot command scripts
- **Database**: MongoDB (configured in `config.json`) or SQLite fallback
- **FCA**: `fca-chris/` — custom local Facebook Chat API package (local `file:./fca-chris`)

## Configuration

- `config.json` — main config (Facebook account, dashboard settings, bot options)
- `configCommands.json` — command-specific config
- `account.txt` — Facebook session state (fbstate/cookies)

## Key Settings

- Dashboard port: reads `process.env.PORT` first, falls back to `config.dashBoard.port` (5000)
- Dashboard binds to `0.0.0.0` — works on Replit, Render, Railway, VPS, Koyeb, Glitch
- Health check endpoints: `/health`, `/ping`, `/alive` — for uptime monitors
- Outgoing bot text is globally converted to the single configured bold serif Unicode style via `global.utils.applyGlobalFontStyle(api)`
- Database: MongoDB — URI from `MONGODB_URI` / `MONGO_URL` env var or `config.json`
- Language: English (`en`)
- Prefix: `)`

## Deployment

Works on all platforms without code changes:
| Platform | Auto-detected env var |
|---|---|
| Render | `RENDER_EXTERNAL_URL` |
| Railway | `RAILWAY_PUBLIC_DOMAIN` |
| Glitch | `PROJECT_DOMAIN` |
| Replit | `REPL_OWNER` / `REPL_SLUG` |
| VPS / custom | Set `BASE_URL` env var |

Deployment files: `render.yaml`, `Procfile`, `railway.json`, `nixpacks.toml`

## Custom FCA Package (`fca-azadx69x`)

The original `@dongdev/fca-unofficial` and `ws3-fca` npm packages have been replaced with a fully custom local package named `fca-azadx69x` (located at `./fca-azadx69x`). It is referenced as `file:./fca-azadx69x` in `package.json`.

Key fixes made to the custom FCA:
1. `module.exports = { login }` (was `module.exports = login`) to match destructuring in login.js
2. `intentionalStop = false` reset at the start of `listenMqtt()` so MQTT can reconnect after `stopListening()`
3. `handleParsedMessage` handles both `data.delta` (nested) and `data.class` (direct) structures from Facebook's MQTT API

## Running

```bash
npm start        # production
npm run dev      # development mode
```

## Replit Setup

- Workflow: `PORT=5000 npm start`
- Web dashboard listens on `0.0.0.0:5000`
- In non-interactive Replit runs, missing Facebook credentials no longer block startup; the dashboard opens so credentials/session state can be added from the UI.

## Deployment

- Type: **VM** (always-running bot process)
- Run command: `node index.js`

## Dependencies

- Node.js 20.x (Replit environment)
- System libraries: `util-linux`, `cairo`, `pango`, `libjpeg`, `giflib`, `librsvg`, `pkg-config` (required for `canvas` package)
- npm packages: see `package.json`

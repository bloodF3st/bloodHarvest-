# BloodHarvest

Telegram userbot (MTProto). Runs on behalf of a user account, not a bot. Supports multiple sessions simultaneously.

Download binary from [Releases](../../releases).

---

## Commands

| Command | Description |
|---|---|
| `.help` | Help |
| `.id` | Chat ID / User ID |
| `.uptime` | Node info & ping |
| `.sp <args>` | Spammer |
| `.tag <args>` | Tag spammer |
| `.sa <args>` | Auto-reply |
| `.gsa <args>` | Global auto-reply |
| `.list [*]` | Active tasks |
| `.t <args>` | Text / photo flood bypass |
| `.logs [chat_id]` | Chat logging to Saved Messages |
| `.timer <args>` | Activity monitor |
| `.watch <args>` | Message watcher |
| `.mute [*]` | Local mute |
| `.pic [*]` | Media for .help / .uptime |
| `.upl` | Upload media to x0.at |
| `.file [*]` | Manage .txt templates |
| `.compile [*]` | Collect messages into one |
| `.title [text]` | Bot header |
| `.sym [text]` | Bot symbol |
| `.dd <N>` | Delete N own messages in chat |
| `.clear <chat_id>` | Delete all tasks in chat |
| `.kill` | Stop all tasks & clear data (confirmation required) |
| `.kal` | Toggle scheduled messages |
| `.renew` | Manually recreate festival bot token |

---

## Environment Variables

| Variable | Required | Description |
|---|---|---|
| `TELEGRAM_API_ID` | ✅ | API ID from my.telegram.org |
| `TELEGRAM_API_HASH` | ✅ | API Hash from my.telegram.org |
| `SESSIONS` | ✅ | Session names comma-separated (e.g. `MAIN`) |
| `PHONE_<NAME>` | ✅ | Phone number per session (e.g. `PHONE_MAIN=+79001234567`) |
| `DATABASE_URL` | ✅ | SQLite path (e.g. `sqlite:data/harvest.db`) |
| `REDIS_URL` | ❌ | Redis URL (for .timer) |
| `RECONNECT_BASE_SECS` | ❌ | Base reconnect delay in seconds (default: `3`) |
| `RECONNECT_MAX_SECS` | ❌ | Max reconnect delay in seconds (default: `60`) |
| `HEALTHCHECK_SECS` | ❌ | Connection check interval in seconds (default: `30`) |
| `USER_TEMPLATES_DIR` | ❌ | Directory for .txt templates (default: `data/user_templates`) |
| `FESTIVAL_BOT_USERNAME` | ❌ | `@username` of festival bot — enables token watchdog |
| `FESTIVAL_BOT_DISPLAY_NAME` | ❌ | Display name for recreated bot (default: `BloodFestival`) |
| `FESTIVAL_BOT_USERNAME_PREFIX` | ❌ | Username prefix (default: `bfest`) → `bfest_<ts><rnd>bot` |
| `FESTIVAL_ENV_PATH` | ❌ | Path to festival bot .env (default: `/opt/bloodfestival/.env`) |
| `FESTIVAL_DB_PATH` | ❌ | Path to festival bot SQLite (default: `/opt/bloodfestival/data.db`) |
| `FESTIVAL_SERVICE` | ❌ | systemd service name (default: `blood-festival-bot`) |
| `FESTIVAL_TOKEN_CHECK_SECS` | ❌ | Token check interval in seconds (default: `15`) |
| `HARVEST_ENV_PATH` | ❌ | Path to this bot's .env (default: `/opt/bloodharvest/.env`) |

---

## Launch

**1. Download binary**

```bash
wget https://github.com/bloodF3st/bloodHarvest-/releases/latest/download/blood-harvest
chmod +x blood-harvest
```

**2. Create `.env`**

```env
TELEGRAM_API_ID=12345678
TELEGRAM_API_HASH=abcdef1234567890abcdef1234567890
SESSIONS=MAIN
PHONE_MAIN=+79001234567
DATABASE_URL=sqlite:data/harvest.db
```

**3. Run**

```bash
./blood-harvest
```

On first launch enter your phone number and the Telegram code — session is saved to a file.

---

## systemd

```ini
[Unit]
Description=BloodHarvest
After=network-online.target

[Service]
Type=simple
WorkingDirectory=/opt/bloodharvest
EnvironmentFile=/opt/bloodharvest/.env
ExecStart=/opt/bloodharvest/blood-harvest
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

```bash
systemctl enable --now blood-harvest
journalctl -u blood-harvest -f
```

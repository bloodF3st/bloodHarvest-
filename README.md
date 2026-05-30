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
| `.logs [chat_id]` | Chat logging |
| `.timer <args>` | Activity monitor |
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
| `.kld <args>` | Scheduled message calendar |
| `.filter` | Toggle SA content filter |

---

## Environment Variables

| Variable | Required | Description |
|---|---|---|
| `TELEGRAM_API_ID` | ✅ | API ID from my.telegram.org |
| `TELEGRAM_API_HASH` | ✅ | API Hash from my.telegram.org |
| `SESSIONS` | ✅ | Phone numbers comma-separated (e.g. `+79001234567`) |
| `DATABASE_URL` | ✅ | SQLite path (e.g. `sqlite:data/harvest.db`) |
| `RECONNECT_BASE_SECS` | ❌ | Base reconnect delay in seconds (default: `3`) |
| `RECONNECT_MAX_SECS` | ❌ | Max reconnect delay in seconds (default: `60`) |
| `HEALTHCHECK_SECS` | ❌ | Connection check interval in seconds (default: `30`) |
| `USER_TEMPLATES_DIR` | ❌ | Directory for .txt templates (default: `data/user_templates`) |
| `BLOODLOGS_TOKEN` | ❌ | bloodLogs bot token — enables redirect to bloodlogs |
| `BLOODLOGS_CHANNEL_ID` | ❌ | Log channel ID for `.logs` redirect |
| `FESTIVAL_BOT_USERNAME` | ❌ | `@username` of festival bot — enables token watchdog |
| `FESTIVAL_ENV_PATH` | ❌ | Path to festival bot .env |
| `FESTIVAL_SERVICE` | ❌ | systemd service name (default: `blood-festival-bot`) |
| `NTFY_URL` | ❌ | ntfy topic URL — enables push notifications (e.g. `https://ntfy.sh/your-topic`) |

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
SESSIONS=+79001234567
DATABASE_URL=sqlite:data/harvest.db
```

**3. Authorize session**

```bash
./blood-harvest --auth +79001234567
```

Enter Telegram code, 2FA password if needed. Session saved to `sessions/`.

**4. Run**

```bash
./blood-harvest
```

---

## Redirect logs and timers to bloodLogs

`.logs` and `.timer` notifications can be sent via [bloodLogs bot](https://github.com/bloodF3st/bloodlogs-bot) instead of Saved Messages.

Add to `.env`:

```env
BLOODLOGS_TOKEN=<bloodlogs bot token from BotFather>
BLOODLOGS_CHANNEL_ID=<log channel chat_id>
```

| Variable | Description |
|---|---|
| `BLOODLOGS_TOKEN` | Token of your bloodLogs bot |
| `BLOODLOGS_CHANNEL_ID` | Same channel configured via `/bchannel` in bloodLogs |

After adding — restart blood-harvest.

**What changes:**
- **`.logs`** — messages from monitored chats go to the bloodLogs channel in unified format
- **`.timer`** — inactivity alerts arrive in your DM from bloodLogs bot:
  ```
  ᴛɪᴍᴇʀ #5: Name 123456789 | Chat -1001234567890 | ɪɴᴀᴄᴛɪᴠᴇ ≥ 2h (ᴛʜʀᴇsʜᴏʟᴅ 1h).
  ʟᴀsᴛ: 2026-05-26 19:42 MSK
  ```

Without these variables — behaviour unchanged (everything goes to Saved Messages).

Both bots work independently. If bloodLogs is unavailable — blood-harvest logs the error and continues.

---

## Push notifications (ntfy)

Add to `.env`:

```env
NTFY_URL=https://ntfy.sh/your-topic
```

Install [ntfy](https://ntfy.sh) on iOS/Android and subscribe to your topic.

| Event | Notification |
|---|---|
| Inactivity timer fired | `⏰ #71: Name (id) \| Chat \| inactive ≥ 1h` |
| Festival token dead | `🔴 @username: 401 Unauthorized` |
| Festival bot recreated | `✅ @new_username created, invites: ok=12 fail=0` |
| Festival renew failed | `⚠️ error details` |

Without `NTFY_URL` — behaviour unchanged.

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

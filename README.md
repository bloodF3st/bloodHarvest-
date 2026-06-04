# BloodHarvest

Userbot на Rust (MTProto через grammers). Работает от имени аккаунта Telegram. Задачи и таймеры хранятся в SQLite — переживают перезапуск. Управление через команды прямо в Telegram (в Saved Messages или любом чате).

---

## Возможности

### Спаммер `.sp`
Отправляет сообщения из `.txt`-шаблона в указанный чат с заданным интервалом. Поддерживает медиа-вложение и префикс перед текстом.
```
.sp <chat_id> <интервал_мс> <файл.txt> [url] [префикс]
.sp del <id>
```

### Теггер `.tag`
Периодически тегает конкретного пользователя в чате — текст из шаблона, опционально с медиа.
```
.tag <chat_id> <user_id> <интервал_мс> <файл.txt> [url] [префикс]
.tag del <id>
```

### Автоответчик `.sa`
Отвечает на сообщения конкретного пользователя в чате — текст из шаблона, с дебаунсом.
```
.sa <chat_id> <user_id> <интервал_мс> <файл.txt> [url] [префикс]
.sa del <id>
```

### Глобальный автоответчик `.gsa`
Отвечает на любое сообщение от целевого пользователя в любом чате. Работает кросс-чатово.
```
.gsa <user_id> <интервал_мс> <файл.txt>
.gsa del <id>
```

### Флуд `.t`
Массовая отправка сообщений (tflood) или фото (pflood) в чат — несколько воркеров параллельно.
```
.t <chat_id> <файл.txt> [префикс]     # tflood
.t <chat_id> + прикреплённое фото      # pflood
.t del <id>
```

### Таймер молчания `.timer`
Отслеживает активность пользователя в чате. Если молчит дольше заданного времени — уведомление.
```
.timer <time>                              # reply на сообщение цели
.timer <user_id> <chat_id> <time>         # вручную
.timer list
.timer del <id>
.timer clear <chat_id>
```
Форматы времени: `30m`, `2h`, `1d`, `90s`.

### Удаление сообщений `.dd`
Удаляет N последних своих сообщений в текущем чате.
```
.dd <N>
```

### Мут `.mute`
Локальный игнор пользователя — бот не будет реагировать на его сообщения.
```
.mute + <user_id>
.mute - <user_id>
```

### Логгер `.logs`
Пересылает все сообщения из чата (или от конкретного пользователя) в Saved Messages.
```
.logs <chat_id>
.logs <chat_id> <user_id>
.logs del <id|chat_id>
```

### Очистка чата `.clear`
Останавливает все задачи определённого типа в чате или все разом.
```
.clear <chat_id>
.clear sp <chat_id>
.clear sa <chat_id>
.clear tag <chat_id>
.clear timer <chat_id>
```

### Список задач `.list`
Все активные задачи с ID для управления.
```
.list
.list sp
.list sa
.list tag
.list timer
```

### Шаблоны `.file`
Управление `.txt`-файлами шаблонов: загрузка (reply на документ), удаление, список.
```
.file list
.file del <файл.txt>
```

### Загрузка медиа `.upl`
Загружает прикреплённое фото/видео/стикер на x0.at и возвращает ссылку.
```
.upl                          # прикрепи медиа к команде
.upl                          # или reply на сообщение с медиа
```

### Медиа для команд `.pic`
Устанавливает картинку которая прикрепляется к ответам `.uptime` / `.help` / `.id`.
```
.pic uptime [url]
.pic help [url]
.pic id [url]
```

### Символ `.sym`
Меняет символ-префикс (по умолчанию `⛧`) во всех ответах бота.
```
.sym ☽
```

### Токен-страж (renew)
Автоматически отслеживает токен blood-festival-bot. При смерти токена:
1. Пишет через BotFather — создаёт новый бот
2. Обновляет `.env` blood-festival-bot и blood-harvest
3. Перезапускает `systemctl restart blood-festival-bot`
4. Приглашает нового бота во все чаты где работал старый
5. Уведомляет в ntfy

---

## Переменные окружения

| Переменная | Обязательно | Описание |
|---|---|---|
| `TELEGRAM_API_ID` | ✅ | API ID с [my.telegram.org](https://my.telegram.org) |
| `TELEGRAM_API_HASH` | ✅ | API Hash |
| `SESSIONS` | ✅ | Номера телефонов через запятую: `+79001234567,+79009876543` |
| `DATABASE_URL` | ✅ | SQLite путь: `sqlite:data/harvest.db` |
| `USER_TEMPLATES_DIR` | — | Папка шаблонов (по умолчанию `data/user_templates`) |
| `NODE_ID` | — | UUID инстанса (произвольный) |
| `NODE_ROLE` | — | `primary` / `secondary` |
| `RECONNECT_BASE_SECS` | — | Базовая задержка реконнекта (по умолчанию `3`) |
| `RECONNECT_MAX_SECS` | — | Максимальная задержка реконнекта (по умолчанию `60`) |
| `HEALTHCHECK_SECS` | — | Интервал проверки соединения (по умолчанию `30`) |
| `NTFY_URL` | — | URL канала ntfy для уведомлений, напр. `https://ntfy.sh/MyChannel` |
| `BLOODLOGS_TOKEN` | — | Токен bloodlogs-bot для логирования событий |
| `BLOODLOGS_CHANNEL_ID` | — | ID канала логов |
| `FESTIVAL_BOT_USERNAME` | — | Username бота blood-festival для token-страж механизма |
| `FESTIVAL_BOT_DISPLAY_NAME` | — | Отображаемое имя festival-бота |
| `FESTIVAL_BOT_USERNAME_PREFIX` | — | Префикс для нового username при пересоздании |
| `FESTIVAL_ENV_PATH` | — | Путь к `.env` blood-festival (по умолчанию `/opt/bloodfestival/.env`) |
| `FESTIVAL_DB_PATH` | — | Путь к БД blood-festival |
| `FESTIVAL_SERVICE` | — | Имя systemd-сервиса (по умолчанию `blood-festival-bot`) |
| `FESTIVAL_TOKEN_CHECK_SECS` | — | Интервал проверки токена в секундах (по умолчанию `420`) |
| `HARVEST_ENV_PATH` | — | Путь к собственному `.env` для обновления |
| `MALLOC_CONF` | — | Настройки аллокатора памяти (jemalloc) |

---

## Установка и запуск

### 1. Скачать бинарник

Скачай актуальный релиз со страницы [Releases](../../releases) — файл `blood-harvest`.

### 2. Разместить файлы

```bash
mkdir -p /opt/bloodharvest/data /opt/bloodharvest/data/user_templates
cp blood-harvest /opt/bloodharvest/
chmod +x /opt/bloodharvest/blood-harvest
```

### 3. Создать `.env`

```bash
cat > /opt/bloodharvest/.env << 'EOF'
TELEGRAM_API_ID=12345678
TELEGRAM_API_HASH=abcdef1234567890abcdef1234567890
SESSIONS=+79001234567
DATABASE_URL=sqlite:data/harvest.db
USER_TEMPLATES_DIR=data/user_templates
NODE_ID=00000000-0000-0000-0000-000000000001
NODE_ROLE=primary
RECONNECT_BASE_SECS=3
RECONNECT_MAX_SECS=60
HEALTHCHECK_SECS=30
NTFY_URL=https://ntfy.sh/MyChannel
MALLOC_CONF=background_thread:true,dirty_decay_ms:0,muzzy_decay_ms:0
EOF
```

### 4. Первый запуск — авторизация

```bash
cd /opt/bloodharvest
./blood-harvest
# Введи код из Telegram при запросе
```

Сессия сохранится в `sessions/+79001234567.session`. После этого бот работает без повторной авторизации.

### 5. Настройка systemd

```bash
cat > /etc/systemd/system/blood-harvest.service << 'EOF'
[Unit]
Description=blood harvest bot
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
WorkingDirectory=/opt/bloodharvest
EnvironmentFile=/opt/bloodharvest/.env
ExecStart=/opt/bloodharvest/blood-harvest
Restart=on-failure
RestartSec=10
StartLimitIntervalSec=120
StartLimitBurst=5

MemoryMax=400M
MemoryHigh=300M
CPUQuota=40%
TasksMax=512

[Install]
WantedBy=multi-user.target
EOF

systemctl daemon-reload
systemctl enable blood-harvest
systemctl start blood-harvest
```

### 6. Управление

```bash
systemctl status blood-harvest     # статус
systemctl restart blood-harvest    # перезапуск
journalctl -u blood-harvest -f     # живые логи
journalctl -u blood-harvest -n 100 # последние 100 строк
```

---

## Несколько инстансов

Для второго аккаунта — отдельная папка и отдельный сервис:

```bash
mkdir -p /opt/bloodharvest2/data /opt/bloodharvest2/data/user_templates
cp /opt/bloodharvest/blood-harvest /opt/bloodharvest2/
# Создай /opt/bloodharvest2/.env с другим SESSIONS=+79009876543
# Скопируй blood-harvest2.service с WorkingDirectory=/opt/bloodharvest2
```

---

## Шаблоны `.txt`

Каждая строка файла — отдельное сообщение. Бот случайно выбирает строку при каждой отправке.

Загрузка шаблона: прикрепи `.txt` файл и напиши `.file` в reply.

Путь к файлам: `USER_TEMPLATES_DIR/<session>/<owner_id>/файл.txt`

---

## Требования к серверу

- Linux x86-64
- glibc 2.17+
- ~50 МБ RAM на инстанс в idle, до 400 МБ под нагрузкой
- SQLite (встроен в бинарник, отдельно не нужен)

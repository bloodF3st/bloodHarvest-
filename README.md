# BloodHarvest

Telegram юзербот (MTProto, userbot-режим). Работает от имени аккаунта, не бота. Поддерживает несколько сессий одновременно.

## Команды

| Команда | Описание |
|---|---|
| `.help` | Справка |
| `.id` | ID чата и пользователя |
| `.uptime` | Uptime процесса и системы |
| `.sp <args>` | Спамер |
| `.tag <args>` | Теггер |
| `.sa <args>` | Автоответчик |
| `.watch <args>` | Слежка за сообщениями |
| `.mute <args>` | Мут пользователя |
| `.upl <args>` | Загрузка медиа |
| `.pic <args>` | Медиа к .help / .uptime |
| `.file <args>` | Шаблоны .txt |
| `.list` | Список активных задач |
| `.compile <args>` | Компиляция шаблонов |

## Установка

1. Скачай бинарник из [Releases](../../releases)
2. Создай `.env` на основе `.env.example`
3. Запусти:

```bash
chmod +x blood-harvest
./blood-harvest
```

При первом запуске введи номер телефона и код из Telegram — сессия сохранится в файл.

## Переменные окружения

| Переменная | Обязательно | Описание |
|---|---|---|
| `TELEGRAM_API_ID` | ✅ | API ID с my.telegram.org |
| `TELEGRAM_API_HASH` | ✅ | API Hash с my.telegram.org |
| `SESSIONS` | ✅ | Имена сессий через запятую (напр. `MAIN,ALT`) |
| `PHONE_<NAME>` | ✅ | Номер телефона для каждой сессии (напр. `PHONE_MAIN=+79001234567`) |
| `DATABASE_URL` | ✅ | Путь к SQLite файлу (напр. `sqlite://data/harvest.db`) |
| `RECONNECT_BASE_SECS` | ❌ | Базовая задержка переподключения в сек (по умолч. `3`) |
| `RECONNECT_MAX_SECS` | ❌ | Максимальная задержка переподключения в сек (по умолч. `60`) |
| `HEALTHCHECK_SECS` | ❌ | Интервал проверки соединения в сек (по умолч. `30`) |
| `HELP_HEADER` | ❌ | Заголовок для `.help` и стартового сообщения |
| `SEND_STARTUP_HELP_HEADER` | ❌ | Отключить стартовое сообщение: `0` |
| `FAKE_MACHINE_UPTIME_START` | ❌ | Стартовый аптайм машины для `.uptime` |
| `FAKE_BOT_UPTIME_START` | ❌ | Стартовый аптайм бота для `.uptime` |
| `USER_TEMPLATES_DIR` | ❌ | Папка с шаблонами `.txt` (по умолч. `data/user_templates`) |

## Зависимости рантайма

Бинарник скомпилирован статически (rustls, без OpenSSL). Никаких системных зависимостей не требуется.

- **OS**: Linux x86_64 (Ubuntu 20.04+, Debian 11+)
- **glibc**: ≥ 2.31
- **SQLite**: встроена в бинарник, ничего устанавливать не нужно

## Стек

| Библиотека | Версия | Назначение |
|---|---|---|
| [grammers-client](https://github.com/Lonami/grammers) | 0.7.0 | Telegram MTProto клиент |
| [tokio](https://tokio.rs) | 1 | Async runtime (multi-thread) |
| [sqlx](https://github.com/launchbadge/sqlx) | 0.8 | SQLite (async, migrate) |
| [reqwest](https://github.com/seanmonstar/reqwest) | 0.12 | HTTP-клиент (rustls, multipart) |
| [anyhow](https://github.com/dtolnay/anyhow) | 1 | Обработка ошибок |
| [dotenvy](https://github.com/allan2/dotenvy) | 0.15 | Загрузка `.env` |
| [chrono](https://github.com/chronotope/chrono) | 0.4 | Дата/время + serde |
| [serde](https://serde.rs) | 1 | Сериализация (derive) |
| [rand](https://github.com/rust-random/rand) | 0.8 | Случайные числа |
| [futures](https://github.com/rust-lang/futures-rs) | 0.3 | Async утилиты |
| [tempfile](https://github.com/Stebalien/tempfile) | 3 | Временные файлы |

## Запуск через systemd

```ini
[Unit]
Description=BloodHarvest Telegram Userbot
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

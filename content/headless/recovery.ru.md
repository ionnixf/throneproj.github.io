+++
title = "Защита от потери доступа и восстановление"
description = "Как headless Throne сохраняет доступ к своему API и как вернуть доступ, если сохранённые настройки, например kill switch или TUN, лишили вас его."
weight = 50
toc = true
+++

## Как Throne остаётся доступным {#protection}

- **API всегда включён**, с первого запуска, на `127.0.0.1:9095`. [Параметры](@/headless/command_line.ru.md#options) `--api-*` переопределяют сохранённые настройки API, а при восстановлении настроек сохраняются настройки API, входящих подключений, TUN и kill switch этого устройства.
- **Файл ключа.** Вместо пустого `api.key` создаётся новый ключ. Любой другой файл ключа, который нельзя использовать, оставляет API выключенным: удалите этот файл, и при следующем запуске будет создан новый ключ.
- **Порт API.** Если порт занят другой программой, Throne работает без API (`api_unavailable`) и пробует снова каждые 30 секунд. Если порт занят другим Throne, укажите этому Throne другой порт через `--api-listen PORT`.
- **Неудачные попытки входа** никогда не блокируют loopback, а также адрес, с которого клиент успешно вошёл за последние 10 минут.
- **Kill switch** всегда пропускает loopback, а на других адресах — ответы с порта API. В Docker без root (rootless) и в других контейнерах с пространством имён пользователей без `CAP_SETGID` он переходит в закрытое состояние (fail closed, `unsupported`), и профили отклоняются, пока вы не выключите настройку `kill_switch`.
- **TUN в Linux** может перехватывать ответы удалённым клиентам API, если не включён `tun_auto_redirect`, а строгий маршрут без `tun_ipv6` отбрасывает клиентов IPv6. Throne один раз предупреждает, если TUN запускается, пока API слушает сетевой адрес.
- **Профиль, из-за которого Throne падает** три запуска подряд в течение минуты после возобновления, больше не возобновляется (`resume_suppressed`). Запустите его командой `thronectl connect`.
- **Ядро, которое постоянно падает** три запуска подряд, больше не приводит к завершению Throne с кодами 70 или 71. Throne остаётся запущенным вместе с API (`core_gave_up`) и снова пробует запустить ядро каждые 5 минут.

## Восстановление доступа {#recovery}

Если сохранённые настройки всё же лишают вас доступа — например, kill switch обрывает ваш сеанс SSH или TUN перехватывает ответы API, — запустите Throne один раз с `--no-resume --no-kill-switch` (или с переменными `THRONE_RESUME=0` и `THRONE_KILL_SWITCH=0`). Ни один профиль не запускается, kill switch остаётся выключенным, и `thronectl` на том же устройстве может исправить настройку, например командой `thronectl settings set kill_switch=false` или `thronectl tun off`. Затем снова запустите Throne обычным образом.

**systemd:**

```bash
printf 'THRONE_RESUME=0\nTHRONE_KILL_SWITCH=0\n' | sudo tee -a /etc/throne/throne.env >/dev/null
sudo systemctl restart throne
thronectl settings set kill_switch=false        # или: thronectl tun off
# снова удалите эти две строки из /etc/throne/throne.env, затем:
sudo systemctl restart throne
```

**Docker:** добавьте `THRONE_RESUME: "0"` и `THRONE_KILL_SWITCH: "0"` в `environment:` в `compose.yaml` и выполните `docker compose up -d`. Исправьте настройку командой `docker compose exec throne thronectl …`, удалите переменные и снова выполните `docker compose up -d`.

**macOS:** добавьте обе переменные в `EnvironmentVariables` в plist и снова загрузите его. Исправьте настройку, удалите переменные и снова загрузите plist:

```bash
sudo launchctl bootout system/moe.Throne.headless
sudo launchctl bootstrap system /Library/LaunchDaemons/moe.Throne.headless.plist
```

**Служба Windows:** остановите её командой `sc.exe stop Throne`, затем вручную запустите Throne из командной строки с правами администратора:

```bat
start "" /wait "C:\Program Files\Throne\Throne.exe" --headless --data-dir C:\ProgramData\Throne --no-resume --no-kill-switch
```

Исправьте настройку с помощью `thronectl` из другой командной строки с правами администратора (см. [Команды из командной строки](@/headless/installation.ru.md#windows-prompt)), остановите Throne клавишами `Ctrl+C` и выполните `sc.exe start Throne`.

**Начать заново:** остановите Throne и переместите его каталог данных в другое место (для системного юнита — `/var/lib/throne`). При следующем запуске будет создан новый пустой каталог с новым ключом API. Восстановите профили из [резервной копии](@/headless/thronectl.ru.md#from-desktop).

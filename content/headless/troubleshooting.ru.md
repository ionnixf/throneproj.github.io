+++
title = "Устранение неполадок"
description = "Типичные проблемы с headless Throne — от юнита, который не запускается, до конфликтов TUN и отклонённых запросов к API, — и что указать в отчёте об ошибке."
weight = 60
toc = true
+++

Начните с `Throne --check`, `Throne --health` и последних строк лога (см. [Проверки состояния и логи](@/headless/monitoring.ru.md)). Строка `EXIT <code>: <reason>` сообщает, почему Throne завершился; см. [Коды завершения](@/headless/command_line.ru.md#exit-codes). Большинство сообщений об ошибках сами говорят, как их исправить. В таблице собраны те, что не говорят.

## Типичные проблемы {#symptoms}

| Симптом | Решение |
| --- | --- |
| Код завершения 3: `… is in use by …`, или приложение для компьютера сообщает `Throne работает в фоновом режиме` (Throne is running in the background) | Два экземпляра Throne используют один каталог данных, например приложение для компьютера и пользовательский юнит. Остановите один из них (`systemctl --user stop throne`) или управляйте headless Throne через `thronectl`. См. [Работа рядом с Throne для компьютера](@/headless/_index.ru.md#beside-desktop). |
| `--print-connection`: `The API key is not in a file` | Ключ хранится в самих настройках, как у приложения для компьютера. Передайте этот ключ thronectl (в приложении для компьютера: `Настройки интеграции` (Integration Settings) → `Throne API` → `Копировать для скриптов` (Copy for scripts)) или запустите Throne с `--api-key-file`. |
| thronectl: `forbidden` | [Административный запрос](@/headless/thronectl.ru.md#remote) с другого устройства. Запустите Throne с `--api-admin-lan` или выполните команду на устройстве, где работает Throne. |
| thronectl: нет подключения | Throne не запущен, или `THRONE_ADDR` указывает на другой адрес. Проверьте `systemctl status throne` и `Throne --print-connection`. |
| `thronectl tun on`: `needs_privileges` | Выполните `Throne --check` и см. [Права в Linux](@/headless/installation.ru.md#linux-privileges). |
| `tun_conflict` | Диапазон TUN Throne использует другая сеть: выберите свободный диапазон, например командой `thronectl settings set tun_ipv4_cidr=172.29.0.1/24`. TUN использует другой Throne или VPN: TUN может работать только у одного, поэтому остановите его. `dns_in_port` занят другой программой: задайте другой порт. Затем снова выполните `thronectl tun on`. |
| Профили отклоняются: `kill_switch_inactive` | Kill switch включён, но не может работать. Причину сообщает `kill_switch_error` в `thronectl --json status`: `privilege`, `unsupported`, `config` или `overridden`. Выключить его можно командой `thronectl settings set kill_switch=false`. См. [Kill Switch](@/advanced/kill_switch.ru.md). |
| `degraded: vpn_challenge_pending` или `vpn_auth_failed` | VPN-профилю нужен вход, который headless Throne не может выполнить, или его сохранённые учётные данные отклонены. Добавьте профилю [запись OTP](@/advanced/vpn_profiles.ru.md#otp-manager) или исправьте учётные данные в Throne для компьютера и перенесите их с помощью [резервной копии](@/headless/thronectl.ru.md#from-desktop). |
| Другие значения `degraded:` | См. [список значений](@/headless/monitoring.ru.md#health). |
| Docker: опубликованный порт 2080 не отвечает | Входящее подключение слушает `127.0.0.1`. Выполните `thronectl settings set inbound_address=0.0.0.0` и снова запустите профиль. |
| Docker: контейнер снова запускается после `thronectl shutdown` | Его снова запускает `restart: unless-stopped`. Используйте `docker compose stop`. |
| macOS: Throne запускается каждые 10 секунд и завершается | launchd повторяет запуск при ошибке конфигурации. Прочитайте `/Library/Logs/Throne/throne.log`. |
| Windows: служба сразу останавливается | `sc.exe query Throne` показывает код завершения Throne как `SERVICE_EXIT_CODE`; лог находится в `C:\ProgramData\Throne\config\logs`. Код 78 часто означает проблему с [папкой данных](@/headless/installation.ru.md#windows-data-folder). |
| Windows: `tun_failed` с `open existing adapter` | Windows всё ещё держит адаптер TUN от запуска, который завершился некорректно. Выполните `thronectl connect last`; если ошибка повторяется, перезагрузите Windows. |
| Вы потеряли доступ из-за kill switch или TUN | Запустите Throne один раз с `--no-resume --no-kill-switch`. См. [Восстановление доступа](@/headless/recovery.ru.md#recovery). |

## Что указать в отчёте об ошибке {#reporting}

Если проблема не исчезает, [сообщите об ошибке](@/help/bug_reports.ru.md) и укажите:

- вывод `Throne --check` и `Throne --health`,
- как работает Throne: пакет или образ, его версию и менеджер служб,
- лог от запуска до появления проблемы, с `--debug`, если можете воспроизвести проблему, а при сбое также `config/logs/crashed-<time>.log`.

Перед публикацией удалите адреса серверов, URL подписок и ключи. Никогда не публикуйте `api.key`.

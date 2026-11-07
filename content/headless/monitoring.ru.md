+++
title = "Проверки состояния и логи"
description = "Как проверить, может ли компьютер запустить headless Throne, узнать состояние работающего Throne и найти его логи."
weight = 40
toc = true
+++

## Throne --check {#check}

`Throne --check` сообщает, может ли этот компьютер запустить headless Throne в текущей настройке. Запускайте эту команду от имени пользователя Throne, с теми же параметрами и переменными, с которыми запускается Throne. Она ничего не меняет и может работать одновременно с запущенным Throne.

```text
$ sudo /opt/Throne/Throne --check
[INFO] data dir /var/lib/throne (root default): does not exist yet; the first start creates it
[ OK ] port 127.0.0.1:9095 (the Throne API, the headless default): free
...
result: 0 failed, 0 warnings
```

Она проверяет каталог данных, настройки, ядро, TUN, kill switch, ключ API, порты, режим перезапуска и [идентификатор устройства](@/headless/_index.ru.md#device-id) и выводит строки `fix:` там, где есть способ исправления. `[FAIL]` получает только то, что вообще не даёт Throne работать, и тогда команда завершается с кодом 1. Нехватка прав для TUN или kill switch — это `[WARN]` с командами `setcap` или `chmod u+s` для исправления либо, в контейнере, с `--cap-add NET_ADMIN` и `--device /dev/net/tun`.

## Throne --health {#health}

`Throne --health` обращается к работающему Throne и выводит одну строку JSON. Запускайте эту команду от имени пользователя Throne и для того же каталога данных:

```bash
$ sudo /opt/Throne/Throne --health
{"api":"listening","core_restarts":0,"degraded":null,"headless_dialogs":0,"ok":true,"previous_crash":false,"previous_exit":{"at":1760700000,"code":75,"reason":"restart requested"},"ready":true,"state":"running","stuck_s":0}
```

Команда завершается с кодом 0, если Throne исправен; остальные коды см. в разделе [Коды завершения](@/headless/command_line.ru.md#exit-codes). Образ Docker использует её как проверку состояния.

| Поле | Значение |
| --- | --- |
| `ok` | Throne запустился, ядро подключено, ничего не зависло, а kill switch выключен или работает. Значение никогда не зависит от удалённого сервера или запущенного профиля, поэтому перезапуск Throne при значении `false` не зацикливается во время сбоя на сервере. |
| `ready` | Throne завершил запуск. |
| `state` | `starting`, `idle`, `connecting`, `running` или `stopping`. |
| `degraded` | `null` или причина, по которой Throne не может работать в текущей настройке (см. ниже). |
| `stuck_s` | Сколько секунд профиль подключается сверх 90 секунд или останавливается сверх 45. Пока значение больше 0, `ok` равно `false`. |
| `headless_dialogs` | Вопросы, пропущенные из-за отсутствия окна. Значение должно оставаться равным 0; лог называет их в строках `HEADLESS-DIALOG`. |
| `previous_exit` | Как завершился предыдущий запуск, в виде `{code, reason, at}`, или `null`. Если он завершился некорректно, `previous_crash` равно `true`. |
| `api` | Слушатель API: `off`, `listening`, `key_invalid`, `failed`, `retrying` (ждёт появления своего адреса до 30 секунд) или `address_missing`. |

Значения `degraded` в том порядке, в каком Throne сообщает их, если подходят несколько:

| Значение | Что означает |
| --- | --- |
| `core_gave_up` | Ядро постоянно падало. Throne завершается с кодом 70 или, после трёх таких запусков подряд, [остаётся запущенным](@/headless/recovery.ru.md#protection) и снова пробует запустить ядро каждые 5 минут. |
| `core_unavailable` | Процесс ядра не работает. |
| `kill_switch_failed` | Kill switch включён, но не сработал, поэтому профили отклоняются. См. [Kill Switch](@/advanced/kill_switch.ru.md). |
| `api_unavailable` | API включён, но не слушает, например потому что его порт занят другой программой или его файл ключа нельзя использовать. `ok` остаётся `true`. |
| `tun_unavailable` | TUN был включён, когда Throne в последний раз остановился, а сейчас не запускается: Throne не хватает прав или TUN занят чем-то другим ([конфликт TUN](@/headless/_index.ru.md#beside-desktop)). Профиль не запущен. Выполните `thronectl tun off`, затем `thronectl connect`. |
| `resume_suppressed` | Throne не возобновил последний профиль, потому что каждый из последних трёх раз, когда он это делал, Throne или его ядро останавливались в течение минуты. Запустите профиль командой `thronectl connect`. |
| `vpn_auth_failed` | Сервер VPN-профиля отклонил его сохранённые учётные данные. Состояние сбрасывается, когда профиль останавливается. |
| `vpn_challenge_pending` | Сервер VPN-профиля запрашивает то, на что Throne не может ответить сам, например вход через веб. Throne отменяет запрос не позже чем через 5 минут. |

Подробности — в [спецификации API](https://github.com/throneproj/Throne-api/blob/main/spec/api.md#headless-throne). Чтобы получить больше полей, например `pid`, `version` и поля `thronectl status`, скрипт может отправить `HEALTH` в сокет `config/.instance` в каталоге данных (кроме Windows):

```bash
echo HEALTH | sudo socat -t 3 - UNIX-CONNECT:/var/lib/throne/config/.instance
```

## systemd: готовность, статус и watchdog {#systemd}

Поставляемый юнит имеет тип `Type=notify`:

- **`READY=1`** — когда Throne удерживает каталог данных, база данных открыта, API слушает, ядро подключено, а kill switch пришёл в устойчивое состояние; обычно меньше чем за 20 секунд. До этого момента Throne продлевает тайм-аут запуска, поэтому `TimeoutStartSec=90` прерывает только зависший запуск.
- **`STATUS=`**, который показывает `systemctl status`, например `Idle`, `Connected <profile> (Tun)` или `Degraded: <reason>`.
- **`WATCHDOG=1`** — каждые 20 секунд (`WatchdogSec=60`). Если главный цикл зависнет, systemd отправит `SIGABRT` и снова запустит Throne.

`KillMode=mixed` обязателен: systemd отправляет `SIGTERM` только самому Throne, а Throne по порядку останавливает ядро и kill switch и завершается (`TimeoutStopSec=45`). Повторный `SIGTERM` сразу принудительно завершает ядро. Не используйте `systemctl kill`: эта команда отправляет сигналы напрямую ядру и вспомогательной программе kill switch.

## Логи {#logs}

Throne пишет лог в стандартный поток ошибок и в `config/logs/throne.log` в каталоге данных:

| Как работает Throne | Лог |
| --- | --- |
| Системный юнит systemd | `journalctl -u throne -f` |
| Пользовательский юнит systemd | `journalctl --user -u throne -f` |
| Docker | `docker compose logs -f throne` |
| LaunchDaemon в macOS | `/Library/Logs/Throne/throne.log` |
| Служба Windows | `C:\ProgramData\Throne\config\logs\throne.log` |

`thronectl logs -n 500` выводит последние 500 строк через API, а `thronectl logs -f` продолжает выводить новые, в том числе после перезапусков. Это [административный запрос](@/headless/thronectl.ru.md#remote). Throne хранит последние 5000 строк, включая вывод ядра; их показывает и страница `Logs` [веб-интерфейса](@/headless/web.ru.md).

`--debug` добавляет отладочные строки. `--log-format` задаёт формат стандартного потока ошибок: `journal` (приоритеты syslog для journald), `plain` (местное время) или `auto`, который выбирает `journal`, если стандартный поток ошибок направлен в журнал.

Строки, которые стоит искать:

- `EXIT <code>: <reason>`: почему Throne завершился. См. [Коды завершения](@/headless/command_line.ru.md#exit-codes).
- `Ready <n> ms after launch`: запуск завершён.
- `[Resume] …`: был ли снова запущен последний профиль.
- `[Kill switch] Not active (<code>): …`: почему kill switch не работает.
- `HEADLESS-DIALOG`: вопрос, на который никто не смог ответить.

Если запуск завершился некорректно, его лог сохраняется как `config/logs/crashed-<time>.log`, а отчёты о сбоях попадают в `config/crashes/`.

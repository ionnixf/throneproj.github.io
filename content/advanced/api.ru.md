+++
title = "Throne API и thronectl"
description = "Управляйте Throne из скриптов, с помощью горячих клавиш и с других устройств через Throne API и его клиент командной строки thronectl."
weight = 80
toc = true
+++

Throne API позволяет скриптам, программам для горячих клавиш и другим устройствам управлять Throne для компьютера и администрировать его. `thronectl` — клиент командной строки для API, а `thronectl web` предоставляет [веб-интерфейс](@/headless/web.ru.md). Чтобы написать свой клиент, используйте библиотеку для Go и [спецификацию API](https://github.com/throneproj/Throne-api/blob/main/spec/api.md) из репозитория [Throne-api](https://github.com/throneproj/Throne-api).

В [Headless Throne](@/headless/_index.ru.md) API включён с самого начала, см. [Управление через thronectl](@/headless/thronectl.ru.md). В Throne для Android нет Throne API.

## Включение API в Throne {#enable}

1. Откройте `Настройки` (Settings) → `Настройки интеграции` (Integration Settings) (только в расширенном режиме, см. [Простой и расширенный режим](@/get_started/configuration.ru.md#interface-mode)), а затем вкладку `Throne API`.
2. Отметьте `Включить Throne API` (Enable the Throne API). Throne создаст новый ключ.
3. Нажмите `ОК` (OK).

API слушает `127.0.0.1:9095`, поэтому подключаться могут только программы на этом компьютере. Чтобы подключались и другие устройства, выберите для `Прослушивать` (Listen on) значение `Локальная сеть` (Local network) и перечислите устройства в `Разрешенные адреса` (Allowed addresses), например `192.168.1.0/24, 10.0.0.5` (если поле пустое, подключаться можно с любого адреса). Отмечайте `Разрешить управление из локальной сети` (Allow management from the local network), только если эти устройства должны отправлять и [административные запросы](#admin-requests).

`Копировать для скриптов` (Copy for scripts) копирует две настройки, которые читает `thronectl`: `THRONE_ADDR` и `THRONE_KEY`. `Сгенерировать заново` (Regenerate) создаёт новый ключ, и скрипты со старым ключом перестают работать. [Kill switch](@/advanced/kill_switch.ru.md) не блокирует клиентов API.

## Установка thronectl {#install-thronectl}

`thronectl` входит в пакеты Throne `.deb` и `.rpm`, в ZIP-архив для Linux и в образ Docker для headless Throne. В остальных случаях скачайте его для Linux, Windows, macOS или FreeBSD со [страницы релизов Throne-api](https://github.com/throneproj/Throne-api/releases) и поместите в папку, которая указана в `PATH`, или установите через Go:

```bash
go install github.com/throneproj/Throne-api/cmd/thronectl@latest
```

## Подключение thronectl к Throne {#connect}

`thronectl` читает адрес из `THRONE_ADDR` (по умолчанию `127.0.0.1:9095`), а ключ — из `THRONE_KEY` или из файла, указанного в `THRONE_KEY_FILE`. Ключ никогда не передаётся как аргумент, поэтому храните его в файле, который можете читать только вы:

1. В Throne нажмите `Копировать для скриптов` (Copy for scripts).
2. Сохраните в файл только ключ, без `THRONE_KEY=`, например в `~/.config/throne/api-key`. В Linux и macOS выполните для этого файла `chmod 600`.
3. Задайте переменные, например в `~/.bashrc`:

   ```bash
   export THRONE_ADDR=127.0.0.1:9095
   export THRONE_KEY_FILE=~/.config/throne/api-key
   ```

   В PowerShell (`$PROFILE`):

   ```powershell
   $env:THRONE_ADDR = "127.0.0.1:9095"
   $env:THRONE_KEY_FILE = "$HOME\throne-api-key.txt"
   ```

4. Проверьте подключение командой `thronectl status`.

Флаги указываются до или после команды: `--addr host:port` и `--key-file file` имеют приоритет над переменными, `--json` выводит JSON вместо таблицы, а `--timeout 30s` задаёт время ожидания (`0` — ждать столько, сколько нужно Throne). В systemd `thronectl` также берёт ключ из учётных данных `api.key` (`LoadCredential=api.key:/path/to/key`). Для headless Throne см. [Подключение thronectl к Throne](@/headless/thronectl.ru.md#connect).

## Команды {#commands}

`thronectl help <command>` описывает команду. Команды принимают ID, которые показывают списки.

| Команда | Что делает |
| --- | --- |
| `status` | Состояние, запущенный профиль, режимы, профиль маршрутизации, версия Throne, а если TUN занят чем-то другим, то чем именно. |
| `groups` | Выводит список групп с их собственными профилями маршрутизации. |
| `profiles [--group N] [--name REGEX] [--ids 1,2]` | Выводит список профилей с результатами их последних тестов. |
| `connect <id>` | Запускает профиль. `--restart` перезапускает его, если он уже запущен. |
| `connect last` | Запускает работающий профиль, если его нет, то последний, а если нет и его, то запомненный. |
| `connect best [--group N] [--name REGEX] [--max-age 1h]` | Запускает профиль с наименьшей задержкой, предварительно проверив профили, у которых нет результата новее `--max-age`. |
| `disconnect`, `toggle` | Останавливает запущенный профиль; `toggle` выполняет `connect last`, если ничего не запущено. |
| `tun on`, `tun off`, `tun toggle` | Режим TUN. |
| `proxy on`, `proxy off`, `proxy toggle` | Системный прокси. |
| `routes`, `route <id>` | Выводит список профилей маршрутизации; делает профиль активным и перезапускает запущенный профиль. |
| `test latency`, `test ip`, `test speed` | Выполняет URL-тест, тест IP или тест скорости для `--group N` (при необходимости с `--name REGEX`) или для ID профилей. |
| `jobs`, `job <id>`, `cancel <id>` | Выводит список недавних тестов, показывает один, останавливает один. |
| `clear` | Удаляет результаты тестов; профили выбираются так же, как для `test`. |
| `live`, `traffic [--by profile] [--by app] [--since 24h]` | Текущая скорость трафика; суммарный трафик. `--since` принимает и дни, например `7d`. |
| `logs [-n N] [-f]` | Последние `N` строк лога (по умолчанию 200, не больше 1000). `-f` выводит новые строки по мере появления. |
| `import [--group N] FILE` | Импортирует ссылки, подписку или конфигурацию Clash, sing-box, Xray, WireGuard или OpenVPN размером до 900 KiB. `-` читает стандартный ввод; с `--url URL` Throne один раз загружает URL. |
| `sub add <url> [--name NAME]` | Добавляет группу подписки и обновляет её. Параметры: `--no-auto-update`, `--no-update`, `--route ID` (собственный профиль маршрутизации группы), `--import-route` (сохранить профиль маршрутизации из подписки). |
| `sub update <id>…`, `sub update --all` | Обновляет группы подписок или все группы, кроме архивных. |
| `group add <name>`, `group rm <id>` | Добавляет группу; удаляет группу вместе с её профилями. |
| `profile rm <id>…`, `profile rm --group N [--name REGEX]` | Удаляет профили. |
| `settings [get] [KEY…]`, `settings set KEY=VALUE…` | Показывает или меняет [настройки](#settings). |
| `backup create [-o FILE] [--parts LIST]` | Записывает архив `.thrbackup`. |
| `backup restore FILE [--parts LIST] [--yes]` | Показывает содержимое архива, спрашивает подтверждение, восстанавливает архив и перезапускает Throne. Части по умолчанию: `profiles,routes,otp,ip_lists`. |
| `rulesets update` | Заново загружает удалённые наборы правил запущенного профиля. |
| `geo`, `geo update [geoip] [geosite] [--only-missing]` | Показывает геофайлы Xray; заново загружает их. |
| `restart`, `shutdown [--yes]` | Перезапускает Throne; завершает его работу после подтверждения. |
| `web [--listen ADDR] …`, `web passwd` | Предоставляет [веб-интерфейс](@/headless/web.ru.md); задаёт его пароль. |
| `keygen [-o FILE]` | Создаёт новый ключ и выводит его или записывает в новый файл, который можете читать только вы. К Throne не подключается. |
| `call <METHOD> <PATH> [key=value…] [--body JSON]` | Отправляет любой запрос из [спецификации API](https://github.com/throneproj/Throne-api/blob/main/spec/api.md). |
| `completion bash`, `zsh`, `fish` или `powershell` | Выводит скрипт [автодополнения](#completion). |
| `version` | Выводит версию `thronectl`. |

- Без `--group` команды `connect best` и `import` используют группу, которую показывает Throne.
- `--name` — регулярное выражение, которое не учитывает регистр и может совпадать с любой частью имени. Заключайте его в кавычки: `--name 'DE|NL'`.
- `--parts` принимает список через запятую из значений `profiles`, `routes`, `settings`, `otp`, `ip_lists` и `icons`.
- Команды ждут результата, но не дольше `--timeout`; если Throne нужно больше времени, он завершает действие сам. С `--no-wait` команда завершается сразу. Тестам и загрузке геофайлов давайте больше времени, например `--timeout 15m`. Throne выполняет только один тест за раз.
- `route` не меняет группы, у которых есть [собственный профиль маршрутизации](@/guides/routing.ru.md#group-routing). Через API группа получает его только при создании через `sub add`; позже его можно изменить в параметрах группы в Throne.
- Коды завершения: `0` — успех; `1` — ошибка от Throne (`error: <code>: <message>`), неудачное обновление или отрицательный ответ на вопрос; `2` — неверная команда или флаг; `3` — нет подключения, ключ отклонён или файл нельзя прочитать.

### Настройки {#settings}

`thronectl settings set log_level=debug kill_switch=true` меняет настройки. `true`, `false` и числа передаются как есть, всё остальное — как текст. Если хотя бы одно значение неверно, Throne ничего не меняет.

| Область | Настройки |
| --- | --- |
| Запуск | `remember_enable` |
| Логи | `log_level`, `xray_log_level` |
| Смешанный входящий порт (mixed) | `inbound_enabled`, `inbound_address`, `inbound_port` |
| Тесты | `test_url`, `test_concurrency`, `test_timeout_ms` |
| Автоматические обновления | `sub_auto_update`, `sub_respect_server_interval`, `route_auto_update` (в минутах, `0` — выключено) |
| Kill switch | `kill_switch` |
| TUN | `tun_strict_route`, `tun_auto_redirect` (Linux), `tun_mtu`, `tun_ipv6`, `tun_ipv4_cidr`, `tun_ipv6_cidr` |
| Порты и API | `dns_in_port`, `dns_in_lan` ([DNS-сервер для других устройств](@/guides/dns.ru.md#dns-server)), `clash_api_port`, `clash_listen_addr`, `singbox_api_port` |
| DNS и время | `remote_dns`, `direct_dns`, `ntp_enabled`, `ntp_server`, `ntp_interval` |

Большинство изменений вступают в силу при перезапуске запущенного профиля (настройки TUN — при следующем включении режима TUN). `thronectl status` сообщает, когда нужен перезапуск, а `thronectl connect last --restart` выполняет его. Допустимые значения указаны в [таблице настроек](https://github.com/throneproj/Throne-api/blob/main/spec/api.md#settings) спецификации.

## Административные запросы {#admin-requests}

`logs`, `import`, `sub add`, `group add`, `group rm`, `profile rm`, `settings set`, `backup`, `restart` и `shutdown` отправляют административные запросы (`logs` — потому что лог называет посещённые сайты). Остальные команды отправляют запросы управления с тем же ключом.

Throne отвечает на административные запросы только со своего компьютера (`127.0.0.1` или `::1`). С других устройств они завершаются ошибкой `forbidden`, если не включён параметр `Разрешить управление из локальной сети` (для headless Throne — `--api-admin-lan`).

- Архивы резервных копий содержат пароли и ключи ваших профилей и секреты OTP в открытом виде. `thronectl` создаёт их так, что читать их можете только вы; не меняйте это и удаляйте архивы, когда они больше не нужны.
- `backup restore` отклоняет архивы с профилями, которые запускают программы или читают файлы, например профилями дополнительного ядра (Extra Core) (`invalid_archive`). Такие архивы восстанавливайте в окне Throne.
- `backup restore` и `shutdown` спрашивают подтверждение. В скрипте без терминала добавьте `--yes`.
- При восстановлении `settings` сохраняются настройки этого компьютера, например Throne API, порты, TUN и kill switch.

## Примеры {#examples}

```bash
thronectl profiles --group 3 --name 'DE|NL'
thronectl connect 12
thronectl --timeout 2m test latency --group 3
thronectl sub add 'https://sub.example.com/abc' --name Provider
thronectl connect best --max-age 10m   # retest results older than 10 minutes
```

Выполните тест скорости для профилей группы 3, в именах которых есть `DE` или `NL`, а затем подключите самый быстрый (нужен [jq](https://jqlang.org/)):

```bash
best=$(thronectl --json --timeout 15m test speed --group 3 --name 'DE|NL' |
  jq -r '[.results[] | select(.download_bps > 0)] | max_by(.download_bps) | .id // empty')
[ -n "$best" ] && thronectl connect "$best"
```

В PowerShell:

```powershell
$job = thronectl --json --timeout 15m test speed --group 3 --name 'DE|NL' | ConvertFrom-Json
$best = $job.results | Where-Object download_bps -gt 0 | Sort-Object download_bps -Descending | Select-Object -First 1
if ($best) { thronectl connect $best.id }
```

### Подключение и отключение одной клавишей {#example-hotkey}

Назначьте клавише Stream Deck, макропада или программы для горячих клавиш команду `thronectl toggle`. Такие программы часто не читают настройки вашей оболочки: задайте `THRONE_KEY_FILE` (и при необходимости `THRONE_ADDR`) как переменную окружения своей учётной записи или добавьте `--key-file`. В Throne также есть [глобальные горячие клавиши](@/reference/shortcuts.ru.md#global-hotkeys) `Запустить/остановить профиль` (Start/Stop Profile) и `Вкл./выкл. режим TUN` (Toggle Tun Mode).

## Автодополнение в оболочке {#completion}

`thronectl completion <shell>` выводит скрипт, который дополняет команды, флаги и имена настроек, а если Throne отвечает в течение секунды, — и ID профилей, групп, профилей маршрутизации и тестов.

```bash
# bash: in ~/.bashrc (on macOS, ~/.bash_profile)
eval "$(thronectl completion bash)"
# zsh: in ~/.zshrc
autoload -Uz compinit && compinit
source <(thronectl completion zsh)
# fish
thronectl completion fish > ~/.config/fish/completions/thronectl.fish
```

```powershell
# In $PROFILE
thronectl completion powershell | Out-String | Invoke-Expression
```

## Безопасность {#security}

- Тот, у кого есть ключ, управляет подключением, режимами и маршрутизацией Throne и может читать имена, адреса, результаты тестов и трафик ваших профилей. Там, где разрешены [административные запросы](#admin-requests), ключ также даёт доступ к конфигурациям профилей и резервным копиям, которые содержат ваши учётные данные в открытом виде.
- Не указывайте ключ в командных строках и скриптах, которые могут читать другие: используйте файл ключа, который можете читать только вы, или `THRONE_KEY` из хранилища секретов.
- Трафик между `thronectl` и Throne шифруется и аутентифицируется с помощью ключа, но без прямой секретности (forward secrecy): тот, кто узнает ключ, сможет прочитать записанный ранее трафик.
- Если ключ утёк, нажмите `Сгенерировать заново` (Regenerate) и передайте скриптам новый ключ. Для headless Throne см. [Защита от потери доступа и восстановление](@/headless/recovery.ru.md).
- Оставьте для `Прослушивать` значение `Только этот компьютер` (This computer only), если API не нужен вам в сети.
- После 5 неудачных попыток за минуту Throne отклоняет адрес на 60 секунд, в том числе для других клиентов за тем же роутером. Throne никогда не отклоняет `127.0.0.1` и адрес, с которого клиент успешно вошёл за последние 10 минут.
- Каждый запрос, который что-то меняет, Throne записывает в лог вместе с адресом клиента.

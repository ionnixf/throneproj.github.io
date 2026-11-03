+++
title = "Управление через thronectl"
description = "Как подключить thronectl к headless Throne, добавить профили и подписки, перенести конфигурацию из приложения для компьютера с помощью резервной копии, изменить настройки и управлять Throne с другого устройства."
weight = 20
toc = true
+++

У headless Throne нет окна, поэтому им управляют с помощью `thronectl` — клиента командной строки для [Throne API](@/advanced/api.ru.md). На этой странице показано, как подключить `thronectl` к headless Throne и как настроить с его помощью Throne. Полный список команд и флагов — на странице [Throne API и thronectl](@/advanced/api.ru.md). Чтобы управлять Throne из браузера, см. [Веб-интерфейс](@/headless/web.ru.md).

## Установка thronectl {#install-thronectl}

Пакеты Throne `.deb` и `.rpm` устанавливают `thronectl` как `/usr/bin/thronectl`, а в ZIP-архиве для Linux он лежит рядом с `Throne`. В остальных случаях установите его на компьютер, где работает Throne, и на любой другой компьютер, с которого хотите управлять Throne:

- скачайте исполняемый файл для Linux, Windows, macOS или FreeBSD со [страницы релизов Throne-api](https://github.com/throneproj/Throne-api/releases) или
- соберите его с помощью Go: `go install github.com/throneproj/Throne-api/cmd/thronectl@latest`.

[Образ Docker](@/headless/installation.ru.md#docker) уже содержит `thronectl` в `/usr/local/bin`. В загрузках Throne для Windows и macOS его нет.

Чтобы оболочка дополняла команды, ID профилей и имена настроек, см. [Автодополнение в оболочке](@/advanced/api.ru.md#completion).

## Подключение thronectl к Throne {#connect}

`thronectl` читает адрес Throne из `THRONE_ADDR` (по умолчанию `127.0.0.1:9095`), а ключ — из файла, указанного в `THRONE_KEY_FILE`, из `THRONE_KEY` или из `--key-file`. Сам ключ он никогда не принимает как аргумент.

`Throne --print-connection` выводит обе переменные как строки `export`, но никогда не выводит сам ключ. Запускайте эту команду от имени пользователя, от которого работает Throne, и с тем же `--data-dir`:

```bash
$ sudo /opt/Throne/Throne --print-connection
export THRONE_ADDR=127.0.0.1:9095
export THRONE_KEY_FILE=/var/lib/throne/config/api.key
```

Как это использовать, зависит от того, как работает Throne:

- **Пользовательский юнит systemd** (Throne работает от вашего имени): `eval "$(/opt/Throne/Throne --print-connection)"` задаёт обе переменные. Затем выполните `thronectl status`.
- **Системный юнит, Docker или LaunchDaemon в macOS** (Throne работает от root): файл ключа может читать только root, а `sudo` сбрасывает переменные. Либо скопируйте ключ в собственный файл:

  ```bash
  mkdir -p ~/.config/throne
  sudo install -m 600 -o "$USER" /var/lib/throne/config/api.key ~/.config/throne/api-key
  export THRONE_KEY_FILE=~/.config/throne/api-key
  thronectl status
  ```

  либо запустите `thronectl` от root с файлом ключа: `sudo thronectl --key-file /var/lib/throne/config/api.key status`.
- **Docker:** запустите `thronectl` внутри контейнера: `docker compose exec throne thronectl --key-file /var/lib/throne/config/api.key status`.
- **Служба Windows:** см. [Команды из командной строки](@/headless/installation.ru.md#windows-prompt).

Добавьте строку `export` в файл запуска своей оболочки (например, `~/.bashrc`), чтобы она действовала в каждой новой оболочке.

`--print-connection` завершается с кодом 2, если API выключен или не слушает либо если его ключ хранится не в файле. Последний случай возникает, когда каталог данных взят из Throne для компьютера, который включил API с собственным ключом. Используйте этот ключ как `THRONE_KEY` (в приложении для компьютера: `Настройки интеграции` (Integration Settings) → `Throne API` → `Копировать для скриптов` (Copy for scripts)) или запустите Throne с `--api-key-file`.

## Административные запросы {#admin-requests}

Запросы, которые изменяют то, что хранит Throne, — административные: `import`, `sub add`, `group`, `profile rm`, `settings set`, `backup`, `restart` и `shutdown`. Таков же и `logs`, потому что лог называет сайты, к которым шли подключения. Throne отвечает на них только с собственного устройства, если он не запущен с `--api-admin-lan` (`THRONE_API_ADMIN_LAN=1`). С другого устройства они завершаются ошибкой `forbidden`.

Все остальные запросы, например `status`, `connect`, `tun on` или `test`, работают с любого устройства, которому разрешён доступ к API и у которого есть ключ.

## Профили и подписки {#profiles}

```bash
thronectl import links.txt                        # ссылки для обмена, тело подписки, Clash, sing-box, Xray, WireGuard или OpenVPN
thronectl sub add 'https://sub.example.com/abc' --name Provider
thronectl groups
thronectl profiles --group 3
thronectl connect 12                              # или: thronectl connect best --group 3
thronectl tun on
```

- `thronectl import` читает файл размером до 900 KiB или стандартный ввод, если указать `-`. С `--url URL` Throne один раз загружает URL. Без `--group` профили попадают в группу, которая выбрана в Throne в данный момент.
- `thronectl sub add` добавляет группу подписки, названную по имени хоста из URL, если вы не укажете `--name`, и загружает её. Группа обновляется автоматически, если вы не добавите `--no-auto-update`.
- `thronectl sub add --route ID` задаёт новой группе собственный профиль маршрутизации с ID из `thronectl routes`. `--import-route` сохраняет профиль маршрутизации, который передаёт подписка, и обновляет его при каждом обновлении. См. [Профиль маршрутизации для группы](@/guides/routing.ru.md#group-routing). `thronectl groups` показывает профиль маршрутизации каждой группы в столбце `ROUTE`.
- `thronectl connect best` запускает профиль с наименьшей задержкой, предварительно проверив профили без недавнего результата (`--max-age`, например `--max-age 1h`).
- `thronectl test latency --group 3` проверяет группу. Тесты могут занимать минуты; задайте для них больший `--timeout`, например `thronectl --timeout 5m test latency --group 3`.

Запущенный профиль снова запускается при каждом запуске Throne — с TUN, если TUN был включён, — пока вы не выполните `thronectl disconnect`. Чтобы один раз запустить Throne без него, используйте [`--no-resume`](@/headless/command_line.ru.md#options).

## Перенос конфигурации с компьютера {#from-desktop}

Некоторые вещи проще настроить в Throne для компьютера, например профили маршрутизации, записи OTP или вход в VPN. Настройте их там, а затем перенесите с помощью резервной копии:

```bash
# На компьютере: создайте резервную копию в настройках «Резервное копирование и восстановление» (Backup and Restore) или, если в Throne для компьютера включён Throne API:
thronectl backup create -o desktop.thrbackup
scp desktop.thrbackup box:

# На устройстве с headless Throne (box):
thronectl backup restore desktop.thrbackup
```

- `backup restore` показывает содержимое архива и спрашивает подтверждение перед восстановлением. Без терминала добавьте `--yes`.
- По умолчанию восстанавливаются профили, профили маршрутизации, записи OTP и списки IP. Чтобы восстановить и настройки, добавьте `--parts profiles,routes,otp,ip_lists,settings`.
- Восстанавливайте `profiles` и `routes` вместе, как по умолчанию. Если восстановить только одно из них, группы теряют собственные профили маршрутизации и следуют активному.
- При восстановлении настроек сохраняются настройки, относящиеся к этому устройству: Throne API, адрес и порты входящих подключений, TUN, kill switch, NTP, логирование и пути к файлам.
- После восстановления Throne перезапускается.
- Через API Throne отклоняет архивы с профилями, которые запускали бы программы или читали файлы на его устройстве, например с профилями дополнительного ядра (Extra Core) (`invalid_archive`).
- Архивы резервных копий содержат учётные данные ваших профилей и секреты OTP в открытом виде. Удалите их, когда закончите.

Резервное копирование в приложении для компьютера описано в разделе [Резервное копирование и восстановление](@/guides/backup.ru.md#backup-restore).

## Настройки {#settings}

`thronectl settings` выводит настройки, которые может менять API, с их текущими значениями. `thronectl settings set` меняет одну или несколько из них:

```bash
thronectl settings
thronectl settings get log_level
thronectl settings set kill_switch=true log_level=debug
```

`true`, `false` и числа передаются как есть, всё остальное — как текст. Среди настроек есть те, которые называют `Throne --check` и ошибка `tun_conflict`: диапазоны адресов TUN (`tun_ipv4_cidr`, `tun_ipv6_cidr`), DNS-порт ядра (`dns_in_port`), входящее подключение (`inbound_address`, `inbound_port`), Clash API (`clash_api_port`, `clash_listen_addr`) и API sing-box (`singbox_api_port`). Некоторые настройки применяются только при следующем запуске профиля; какие именно, указано в [таблице настроек](https://github.com/throneproj/Throne-api/blob/main/spec/api.md#settings) в спецификации API.

`thronectl settings set dns_in_lan=true` позволяет другим устройствам в сети, например роутеру, пользоваться DNS-сервером ядра; см. [DNS-сервер для других устройств](@/guides/dns.ru.md#dns-server).

Настройки, которыми API не управляет, можно изменить только в Throne для компьютера. Перенесите их с помощью [восстановления настроек](#from-desktop).

## Управление с другого устройства {#remote}

По умолчанию API слушает только `127.0.0.1`. Чтобы управлять Throne с другого устройства, разрешите API слушать сеть со списком разрешённых адресов, а если это устройство должно импортировать данные и менять настройки, разрешите и административные запросы. Для юнита systemd добавьте следующее в `/etc/throne/throne.env` и выполните `sudo systemctl restart throne`:

```bash
THRONE_API_LISTEN=*:9095
THRONE_API_ALLOW=192.168.1.0/24
THRONE_API_ADMIN_LAN=1
```

На другом устройстве скопируйте ключ в файл, который можете читать только вы, укажите этот файл в `THRONE_KEY_FILE` и задайте `THRONE_ADDR=<box>:9095`.

- [Kill switch](@/advanced/kill_switch.ru.md) всегда выпускает ответы API, поэтому клиенты получают доступ к API, откуда бы они ни подключались.
- Трафик к API зашифрован и аутентифицирован, в том числе в локальной сети. И всё же держите API на loopback, если он не нужен вам в сети, и всегда задавайте список разрешённых адресов.
- После 5 неудачных попыток аутентификации за минуту с одного адреса Throne отклоняет этот адрес на 60 секунд. Клиент со старым ключом может заблокировать других клиентов за тем же NAT.
- В Linux, если TUN запускается, пока API слушает сетевой адрес, Throne один раз предупреждает, если удалённые клиенты могут потерять доступ к API. См. [Как Throne остаётся доступным](@/headless/recovery.ru.md#protection).

## Команды {#commands}

Самые нужные команды:

| Команда | Что делает |
| --- | --- |
| `thronectl status` | Состояние, запущенный профиль, TUN, профиль маршрутизации, версия, возможности и конфликт TUN, если он есть |
| `thronectl groups`, `thronectl profiles [--group N]` | Группы и профили с их ID и результатами последних тестов |
| `thronectl connect <id>`, `connect best`, `connect last` | Запуск профиля |
| `thronectl disconnect`, `thronectl toggle` | Остановка профиля; подключение или отключение |
| `thronectl tun on`, `tun off` | Режим TUN |
| `thronectl routes`, `thronectl route <id>` | Список и активация профилей маршрутизации |
| `thronectl test latency \| ip \| speed --group N` | Тестирование профилей |
| `thronectl sub update --all` | Обновление всех подписок |
| `thronectl geo update`, `thronectl rulesets update` | Повторная загрузка геофайлов и наборов правил |
| `thronectl traffic --by app --since 7d` | Суммарный трафик |
| `thronectl logs -n 50`, `thronectl logs -f` | Последние строки лога Throne; с `-f` — затем новые строки по мере записи. См. [Логи](@/headless/monitoring.ru.md#logs) |
| `thronectl restart`, `thronectl shutdown` | Перезапуск Throne; завершение его работы |

Добавьте `--json` к любой команде, чтобы получить вывод в формате JSON, удобный для скриптов. `thronectl help <command>` описывает команду. Все команды и флаги, а также коды завершения `thronectl` описаны на странице [Throne API и thronectl](@/advanced/api.ru.md#commands).

`thronectl restart`, восстановление резервной копии и `SIGHUP` перезапускают Throne; как именно, зависит от [режима перезапуска](@/headless/command_line.ru.md#restart-modes). `thronectl shutdown` заставляет Throne завершиться с кодом 0. Под systemd, launchd и службой Windows он после этого остаётся остановленным; Docker с `restart: unless-stopped` запускает его снова.

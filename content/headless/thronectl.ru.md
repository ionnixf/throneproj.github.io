+++
title = "Управление через thronectl"
description = "Как подключить thronectl к headless Throne, добавить профили и подписки, перенести конфигурацию с компьютера и управлять Throne с другого устройства."
weight = 20
toc = true
+++

У headless Throne нет окна. Им управляют с помощью `thronectl` — клиента командной строки для [Throne API](@/advanced/api.ru.md) — или из браузера через [веб-интерфейс](@/headless/web.ru.md).

## Подключение thronectl к Throne {#connect}

Пакеты `.deb` и `.rpm` устанавливают `thronectl` как `/usr/bin/thronectl`, в ZIP-архиве для Linux он лежит рядом с `Throne`, а в образе Docker — в `/usr/local/bin`. На macOS, Windows и других компьютерах, с которых вы управляете Throne, [установите thronectl](@/advanced/api.ru.md#install-thronectl) сами.

`thronectl` читает адрес Throne из `THRONE_ADDR` (по умолчанию `127.0.0.1:9095`), а ключ — из файла, указанного в `THRONE_KEY_FILE`, из `THRONE_KEY` или из `--key-file`. Сам ключ он никогда не принимает как аргумент. `Throne --print-connection` выводит обе переменные. Запускайте эту команду от имени пользователя, от которого работает Throne, и с тем же `--data-dir`:

```bash
$ sudo /opt/Throne/Throne --print-connection
export THRONE_ADDR=127.0.0.1:9095
export THRONE_KEY_FILE=/var/lib/throne/config/api.key
```

- **Пользовательский юнит systemd:** `eval "$(/opt/Throne/Throne --print-connection)"`, затем `thronectl status`.
- **Throne работает от root** (системный юнит, LaunchDaemon в macOS): файл ключа может читать только root, а `sudo` сбрасывает переменные. Скопируйте ключ в собственный файл:

  ```bash
  mkdir -p ~/.config/throne
  sudo install -m 600 -o "$USER" /var/lib/throne/config/api.key ~/.config/throne/api-key
  export THRONE_KEY_FILE=~/.config/throne/api-key
  thronectl status
  ```

  Или запустите `thronectl` от root: `sudo thronectl --key-file /var/lib/throne/config/api.key status`.
- **Docker:** `docker compose exec throne thronectl --key-file /var/lib/throne/config/api.key status`.
- **Служба Windows:** см. [Команды из командной строки](@/headless/installation.ru.md#windows-prompt).

Добавьте строку `export` в файл запуска оболочки, например `~/.bashrc`. Чтобы оболочка дополняла команды и ID, см. [Автодополнение в оболочке](@/advanced/api.ru.md#completion).

## Повседневные команды {#profiles}

```bash
thronectl status
thronectl import links.txt               # ссылки, подписка, Clash, sing-box, Xray, WireGuard или OpenVPN
thronectl sub add 'https://sub.example.com/abc' --name Provider
thronectl profiles --group 3
thronectl connect 12                     # или: thronectl connect best --group 3
thronectl tun on
thronectl routes                         # затем: thronectl route <id>
thronectl logs -f
thronectl disconnect
```

- `import` читает файл размером до 900 KiB или стандартный ввод, если указать `-`; с `--url URL` Throne один раз загружает URL. Без `--group` профили попадают в группу, которую показывает Throne.
- `sub add --route ID` задаёт новой группе собственный [профиль маршрутизации](@/guides/routing.ru.md#group-routing); `--import-route` использует профиль, который передаёт подписка.
- `connect best` запускает профиль с наименьшей задержкой, предварительно проверив профили без недавнего результата (`--max-age 1h`).
- Тесты могут занимать минуты, поэтому задайте им больший тайм-аут: `thronectl --timeout 5m test latency --group 3`.
- `thronectl restart` перезапускает Throne так, как задаёт его [режим перезапуска](@/headless/command_line.ru.md#restart-modes). `thronectl shutdown` завершает Throne с кодом 0: systemd, launchd и служба Windows оставляют его остановленным, а Docker с `restart: unless-stopped` запускает снова.

Добавьте `--json`, чтобы получить вывод в JSON; `thronectl help <command>` описывает команду. Все команды и коды завершения: [Команды](@/advanced/api.ru.md#commands).

## Перенос конфигурации с компьютера {#from-desktop}

Записи OTP, вход в VPN и профили маршрутизации проще настроить в Throne для компьютера. Настройте их там, а затем перенесите с помощью резервной копии:

```bash
# На компьютере: создайте резервную копию в настройках «Резервное копирование и восстановление» (Backup and Restore) или, если в Throne для компьютера включён Throne API:
thronectl backup create -o desktop.thrbackup
scp desktop.thrbackup box:

# На устройстве с headless Throne (box):
thronectl backup restore desktop.thrbackup
```

- `backup restore` показывает содержимое архива и спрашивает подтверждение. Без терминала добавьте `--yes`. После восстановления Throne перезапускается.
- По умолчанию восстанавливаются профили, профили маршрутизации, записи OTP и списки IP. Чтобы восстановить и настройки, добавьте `--parts profiles,routes,otp,ip_lists,settings`. При восстановлении настроек сохраняются настройки, относящиеся к этому устройству: Throne API, адрес и порты входящих подключений, TUN, kill switch, NTP, логирование и пути к файлам.
- Восстанавливайте `profiles` и `routes` вместе. Если восстановить только одно из них, группы теряют собственные профили маршрутизации.
- Throne отклоняет архивы с профилями, которые запускали бы программы или читали файлы на его устройстве, например с профилями дополнительного ядра (Extra Core) (`invalid_archive`).
- Резервные копии содержат учётные данные ваших профилей и секреты OTP в открытом виде. Удалите их, когда закончите.

Резервное копирование в приложении для компьютера описано в разделе [Резервное копирование и восстановление](@/guides/backup.ru.md#backup-restore).

## Настройки {#settings}

```bash
thronectl settings
thronectl settings set kill_switch=true log_level=debug
```

`thronectl settings` выводит настройки, которыми управляет API, с их текущими значениями; они описаны в разделе [Настройки](@/advanced/api.ru.md#settings). Остальные можно изменить только в Throne для компьютера: перенесите их с помощью [восстановления настроек](#from-desktop).

## Управление с другого устройства {#remote}

API слушает только `127.0.0.1`. Чтобы управлять Throne с другого устройства, разрешите ему слушать сеть со списком разрешённых адресов. Для юнита systemd добавьте следующее в `/etc/throne/throne.env` и выполните `sudo systemctl restart throne`:

```bash
THRONE_API_LISTEN=*:9095
THRONE_API_ALLOW=192.168.1.0/24
THRONE_API_ADMIN_LAN=1
```

На другом устройстве сохраните ключ в файл, который можете читать только вы, укажите этот файл в `THRONE_KEY_FILE`, а в `THRONE_ADDR` — `<box>:9095`.

- **Административные запросы** — импорт, добавление подписок, удаление, изменение настроек, резервное копирование, чтение лога, перезапуск и завершение — Throne выполняет только с собственного устройства, если не задано `THRONE_API_ADMIN_LAN=1` (`--api-admin-lan`). Иначе они завершаются ошибкой `forbidden`. Не задавайте его, если другие устройства должны только следить за Throne и переключать профили.
- После 5 неудачных попыток аутентификации за минуту с одного адреса Throne отклоняет этот адрес на 60 секунд. Клиент со старым ключом может заблокировать других клиентов за тем же NAT.
- [Kill switch](@/advanced/kill_switch.ru.md) всегда выпускает ответы API. В Linux TUN может перехватить ответы удалённым клиентам; см. [Как Throne остаётся доступным](@/headless/recovery.ru.md#protection).
- Трафик к API зашифрован и аутентифицирован. И всё же держите API на loopback, если он не нужен вам в сети, и всегда задавайте список разрешённых адресов.

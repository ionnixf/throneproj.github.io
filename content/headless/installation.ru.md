+++
title = "Установка и службы"
description = "Как установить headless Throne как службу systemd, пользовательский юнит systemd, контейнер Docker, LaunchDaemon в macOS или службу Windows."
weight = 10
toc = true
+++

Установите Throne, как описано на странице [Установка](@/get_started/installation.ru.md), а затем поручите его запуск менеджеру служб:

| Вариант | От чьего имени работает | Каталог данных |
| --- | --- | --- |
| [Пакет для Linux (юнит systemd)](#linux-packages) | root | `/var/lib/throne` |
| [Пользовательский юнит systemd](#user-unit), любой Linux | ваш пользователь | `~/.config/Throne` |
| [Docker](#docker) | root в контейнере | том `/var/lib/throne` |
| [LaunchDaemon в macOS](#macos) | root | `/Library/Application Support/Throne` |
| [Служба Windows](#windows) | LocalSystem | `C:\ProgramData\Throne` |

Затем [подключите thronectl](@/headless/thronectl.ru.md#connect).

## Debian, Ubuntu, Raspberry Pi OS, Fedora, openSUSE {#linux-packages}

Пакеты `.deb` и `.rpm` со [страницы релизов](https://github.com/throneproj/Throne/releases) и из репозиториев пакетов устанавливают Throne в `/opt/Throne`, юнит systemd `throne.service`, его файл окружения `/etc/throne/throne.env` и `thronectl`. Какой пакет подходит для какой системы, описано в разделе [Linux](@/get_started/installation.ru.md#linux); на Raspberry Pi используйте 64-битную Raspberry Pi OS.

```bash
sudo apt install ./Throne-<version>-debian-amd64.deb   # или: sudo dnf install ./Throne-<version>-fedora-amd64.rpm
sudo /opt/Throne/Throne --check                        # необязательно: может ли этот компьютер его запустить?
sudo systemctl enable --now throne
journalctl -u throne -f
```

- Пакет никогда сам не включает юнит. Обновление перезапускает юнит, если он работает, а удаление пакета останавливает и отключает его.
- `/var/lib/throne` (профили, настройки и ключ API) остаётся после удаления пакета, даже при `apt purge`.
- Чтобы задать переменную, раскомментируйте её в `/etc/throne/throne.env` и выполните `sudo systemctl restart throne`. Обновления сохраняют ваши изменения. Никогда не записывайте туда ключ API; вместо этого укажите файл ключа в `THRONE_API_KEY_FILE`.
- Чтобы изменить командную строку, добавьте drop-in-файл командой `sudo systemctl edit throne`:

  ```ini
  [Service]
  ExecStart=
  ExecStart=/opt/Throne/Throne --headless --data-dir=%S/throne --debug
  ```

- Второй юнит, `throne-web.service`, запускает [веб-интерфейс](@/headless/web.ru.md#systemd). Он тоже не включается.

## Другие способы установки в Linux {#other-linux}

[Скрипт установки](@/get_started/installation.ru.md#linux-script) и [портативный ZIP](@/get_started/installation.ru.md#linux-zip) не устанавливают юнит, а в [пакете AUR](@/get_started/installation.ru.md#aur) его может не быть. Установите юнит из пакета:

```bash
curl -fsSLo throne.service https://raw.githubusercontent.com/throneproj/Throne/dev/dist/linux/throne.service
sudo install -m 644 throne.service /etc/systemd/system/throne.service
sudo systemctl daemon-reload && sudo systemctl enable --now throne
```

Юнит запускает `/opt/Throne/Throne` — туда Throne устанавливает скрипт установки. Для другой папки, например `/usr/lib/throne` в пакете AUR, измените путь drop-in-файлом (`sudo systemctl edit throne`):

```ini
[Service]
ExecStart=
ExecStart=/usr/lib/throne/Throne --headless --data-dir=%S/throne
```

Чтобы получить примеры переменных, сохраните [`dist/linux/throne.env`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne.env) как `/etc/throne/throne.env`.

## Права в Linux {#linux-privileges}

TUN и [kill switch](@/advanced/kill_switch.ru.md) требуют прав. Профилям с [дополнительным ядром (Extra Core)](@/advanced/chains.ru.md#extra-core) нужен обычный пользователь, потому что Throne никогда не запускает дополнительное ядро от root. Выберите, как будет работать юнит:

| Как работает юнит | TUN | Kill switch | Профили Extra Core |
| --- | --- | --- | --- |
| От root (юнит в поставляемом виде) | Да | Да | Нет |
| От `throne` с возможностями (`caps.conf`) | Да | Нет (`unsupported`) | Да |
| От `throne` с `ThroneCore`, у которого setuid-root | Да | Да | Да |
| От `throne` без дополнительных настроек | Нет | Нет | Да |

**Возможности (capabilities).** Скачайте [`caps.conf`](https://github.com/throneproj/Throne/blob/dev/dist/linux/caps.conf), затем:

```bash
sudo useradd --system --no-create-home --home-dir /var/lib/throne --shell /usr/sbin/nologin throne
sudo install -Dm644 caps.conf /etc/systemd/system/throne.service.d/caps.conf
sudo systemctl daemon-reload && sudo systemctl restart throne
```

В этой конфигурации у `ThroneCore` не должно быть ни бита setuid, ни файловых возможностей, потому что запуск такого файла сбрасывает возможности, которые выдаёт юнит.

**Ядро с setuid.** Используйте drop-in-файл, в котором в разделе `[Service]` есть только `User=throne` и `Group=throne`, и сделайте так, чтобы ядро запускалось от root:

```bash
sudo chown root:root /opt/Throne/ThroneCore && sudo chmod u+s /opt/Throne/ThroneCore
```

Обновление пакета заменяет `ThroneCore` файлом без бита setuid. В Debian и Ubuntu команда `sudo dpkg-statoverride --update --add root root 4755 /opt/Throne/ThroneCore` сохраняет бит. Не добавляйте в юнит `NoNewPrivileges=` или параметр усиления защиты, который его подразумевает: он помешает работе ядра с setuid.

`thronectl status` показывает, что может этот Throne, в `capabilities.tun` и `capabilities.kill_switch`.

## Пользовательский юнит systemd {#user-unit}

Без root Throne работает как пользовательский юнит в вашем собственном сеансе. Скачайте [`throne-user.service`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne-user.service), затем:

```bash
install -Dm644 throne-user.service ~/.config/systemd/user/throne.service
systemctl --user daemon-reload && systemctl --user enable --now throne
loginctl enable-linger "$USER"   # запуск при загрузке, без входа в систему
```

- Юнит использует каталог данных приложения для компьютера, `~/.config/Throne`, с теми же профилями и настройками. Сначала закройте приложение для компьютера, а перед тем как снова его открыть, выполните `systemctl --user stop throne`.
- Для TUN и kill switch нужно ядро с setuid-root. Приложение для компьютера настраивает его, когда впервые запрашивает [права](@/guides/tun_mode.ru.md#privileges-linux), а `Throne --check` выводит нужные команды.
- Если запускать `Throne --health`, `--print-connection` и `--check` от имени вашего пользователя, они находят этот Throne без параметров.

## Docker {#docker}

Образ `ghcr.io/throneproj/throne` (тег релиза или `latest`) работает на `linux/amd64` и `linux/arm64`, `thronectl` в нём лежит в `/usr/local/bin`. Начните с файла [`compose.yaml`](https://github.com/throneproj/Throne/blob/dev/docker/compose.yaml) из репозитория Throne:

```bash
curl -fsSLO https://raw.githubusercontent.com/throneproj/Throne/dev/docker/compose.yaml
docker compose up -d
docker compose exec throne thronectl --key-file /var/lib/throne/config/api.key status
```

Его основные части:

```yaml
services:
  throne:
    image: ghcr.io/throneproj/throne:latest   # или тег релиза
    restart: unless-stopped
    stop_grace_period: 45s
    cap_add: [NET_ADMIN]                      # TUN и kill switch
    devices: ["/dev/net/tun:/dev/net/tun"]    # TUN и kill switch
    volumes: ["throne-data:/var/lib/throne"]
    ports: ["2080:2080"]                      # смешанный порт (HTTP и SOCKS)
volumes:
  throne-data:
```

- **Порт прокси.** Смешанный порт слушает `127.0.0.1`, а опубликованный порт до него не доходит: выполните `thronectl settings set inbound_address=0.0.0.0` и снова запустите профиль. Опубликованные порты обходят брандмауэры хоста, например `ufw`; на хосте, доступном из интернета, публикуйте порт только на адресе в локальной сети (`"192.168.1.2:2080:2080"`).
- **Сеть хоста** (`network_mode: host`, без `ports:`): TUN и kill switch действуют на весь хост, и только один Throne на нём может использовать TUN.
- **Остановка.** Оставьте `stop_grace_period: 45s`, иначе Docker может принудительно завершить Throne до того, как он остановит ядро. `restart: unless-stopped` снова запускает Throne даже после `thronectl shutdown`; используйте `docker compose stop`.
- **Состояние.** Проверка состояния запускает `Throne --health`. Docker только помечает контейнер как нездоровый (unhealthy) и не перезапускает его.
- **Ограничения.** Профили Extra Core не работают, потому что контейнер работает от root. В Docker без root (rootless) и других контейнерах с пространством имён пользователей kill switch завершается ошибкой `unsupported`.
- **Параметры.** Переменные задаются в `environment:`, флаги — в `command:` (Docker добавляет их после `--headless`). Меняйте каталог данных только через `THRONE_DATA_DIR`: им пользуется проверка состояния.
- **thronectl.** Задайте `THRONE_KEY_FILE: /var/lib/throne/config/api.key` в `environment:`, и `--key-file` не понадобится. Импортируйте файл командой `docker compose exec -T throne thronectl import - < links.txt`.
- **API из сети.** Задайте `THRONE_API_LISTEN: "*:9095"` и `THRONE_API_ALLOW`, опубликуйте порт 9095 и для импорта и изменения настроек добавьте `THRONE_API_ADMIN_LAN: "1"`, потому что запросы через опубликованный порт приходят извне контейнера. За NAT Docker у всех клиентов один и тот же адрес, поэтому один клиент со старым ключом блокирует остальных на минуту.
- **Логи:** `docker compose logs -f throne`. Веб-интерфейс: см. [Docker](@/headless/web.ru.md#docker).

## macOS {#macos}

LaunchDaemon запускает Throne от root при загрузке, с TUN и kill switch, но без профилей Extra Core. Переместите `Throne.app` в `/Applications`, скачайте [`moe.Throne.headless.plist`](https://github.com/throneproj/Throne/blob/dev/dist/macos/moe.Throne.headless.plist), затем:

```bash
sudo mkdir -p /Library/Logs/Throne
sudo install -m 644 -o root -g wheel moe.Throne.headless.plist /Library/LaunchDaemons/
sudo xattr -dr com.apple.quarantine /Applications/Throne.app   # только для неподписанных сборок
sudo launchctl bootstrap system /Library/LaunchDaemons/moe.Throne.headless.plist
```

- Чтобы остановить и выгрузить его: `sudo launchctl bootout system/moe.Throne.headless`.
- Лог находится в `/Library/Logs/Throne/throne.log`. Чтобы узнать состояние, выполните `sudo /Applications/Throne.app/Contents/MacOS/Throne --health`.
- Никогда не запускайте headless Throne через `open`: эта команда сразу возвращает управление, и launchd теряет процесс.
- launchd снова запускает Throne через 10 секунд после каждого завершения с ошибкой, в том числе после ошибок, которые новый запуск не исправит (коды 3, 64 и 78). Если Throne постоянно запускается и завершается, прочитайте лог.

## Служба Windows {#windows}

`Throne.exe --headless --service` работает как служба Windows под учётной записью LocalSystem, поэтому TUN и kill switch работают.

1. Установите Throne для всех пользователей с помощью установщика в `Program Files`. Служба не должна запускать программу, которую может заменить обычный пользователь, поэтому не используйте установку для одного пользователя или ZIP в папке пользователя.
2. Скачайте [`install-service.bat`](https://github.com/throneproj/Throne/blob/dev/dist/windows/install-service.bat) и [`uninstall-service.bat`](https://github.com/throneproj/Throne/blob/dev/dist/windows/uninstall-service.bat).
3. В командной строке с правами администратора выполните `install-service.bat` или, если Throne находится в другой папке, `install-service.bat "D:\Apps\Throne\Throne.exe"`.

Служба `Throne` запускается после загрузки (отложенный запуск). Её действия восстановления перезапускают Throne через 3, 10, а затем 60 секунд, в том числе после завершения с кодом ошибки, например по запросу перезапуска (код 75) или по watchdog.

- `sc.exe stop Throne` и `thronectl shutdown` останавливают Throne окончательно. Перед обновлением Throne останавливайте службу.
- После ошибки `sc.exe query Throne` показывает собственный код завершения Throne как `SERVICE_EXIT_CODE` (`WIN32_EXIT_CODE` при этом равен `1066`). Лог находится в `C:\ProgramData\Throne\config\logs\throne.log`.
- `uninstall-service.bat` удаляет службу и оставляет данные.

### Папка данных {#windows-data-folder}

Служба оставляет доступ к `C:\ProgramData\Throne` только SYSTEM и администраторам (Administrators). Если папка принадлежит другому пользователю, позволяет другим пользователям записывать в неё (так ведёт себя папка, созданная вручную в `C:\ProgramData`) или сама является ссылкой или junction-точкой либо содержит их, служба завершается с кодом 78 и называет проблему в логе. Удалите папку и снова запустите службу.

### Команды из командной строки {#windows-prompt}

Выполняйте `Throne.exe --health`, `--print-connection`, `--check` и `thronectl` из командной строки с правами администратора, а командам `Throne.exe` передавайте `--data-dir C:\ProgramData\Throne`: администратор — это не LocalSystem, и каталог данных по умолчанию у него другой. Открыть канал (pipe) службы и прочитать её файл ключа могут только администраторы и SYSTEM.

`Throne.exe` — программа с графическим интерфейсом, поэтому командная строка не ждёт её завершения. Чтобы дождаться завершения и прочитать код:

```bat
start "" /wait "C:\Program Files\Throne\Throne.exe" --health --data-dir C:\ProgramData\Throne
echo %ERRORLEVEL%
```

`--print-connection` выводит строки `export` в формате POSIX. В PowerShell задайте значения сами:

```powershell
$env:THRONE_ADDR = '127.0.0.1:9095'
$env:THRONE_KEY_FILE = 'C:\ProgramData\Throne\config\api.key'
thronectl status
```

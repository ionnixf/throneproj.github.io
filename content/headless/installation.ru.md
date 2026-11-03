+++
title = "Установка и службы"
description = "Как установить headless Throne как службу systemd, пользовательский юнит systemd, контейнер Docker, LaunchDaemon в macOS или службу Windows."
weight = 10
toc = true
+++

Headless Throne — это обычная программа Throne, запущенная с `--headless`. Установите Throne, как описано на странице [Установка](@/get_started/installation.ru.md), а затем поручите его запуск менеджеру служб. На этой странице показано, как это сделать в каждой системе:

| Система | Менеджер служб | От чьего имени работает | Каталог данных |
| --- | --- | --- | --- |
| Debian, Ubuntu, Raspberry Pi OS, Fedora, openSUSE | [Системный юнит systemd](#linux-packages) | root (или пользователь `throne`) | `/var/lib/throne` |
| Любой Linux, без root | [Пользовательский юнит systemd](#user-unit) | ваш пользователь | `~/.config/Throne` |
| Docker | [Docker или Docker Compose](#docker) | root в контейнере | том `/var/lib/throne` |
| macOS | [LaunchDaemon в launchd](#macos) | root | `/Library/Application Support/Throne` |
| Windows | [Служба Windows](#windows) | LocalSystem | `C:\ProgramData\Throne` |

После установки подключите `thronectl`, как описано на странице [Управление через thronectl](@/headless/thronectl.ru.md).

## Debian, Ubuntu, Raspberry Pi OS, Fedora, openSUSE {#linux-packages}

Пакеты `.deb` и `.rpm` со [страницы релизов](https://github.com/throneproj/Throne/releases) и из репозиториев пакетов устанавливают Throne в `/opt/Throne`, юнит systemd `throne.service` и файл окружения `/etc/throne/throne.env`. Выбирайте пакет по версии библиотеки C в системе (glibc):

| Пакет | Требует | Работает, например, в |
| --- | --- | --- |
| `Throne-<version>-debian-amd64.deb`, `…-fedora-amd64.rpm` | glibc 2.28 | Debian 10, Ubuntu 20.04, RHEL 8 и новее, Fedora, openSUSE |
| `…-amd64-system-qt.deb`, `…-amd64-system-qt.rpm` | glibc 2.34 и системный Qt 6 | Debian 12, Ubuntu 22.04 и новее, Fedora, openSUSE Tumbleweed, с Qt из дистрибутива |
| `Throne-<version>-debian-arm64.deb`, `…-fedora-arm64.rpm` | glibc 2.28 | Raspberry Pi OS bullseye, Debian 10, Ubuntu 20.04 и новее на arm64 |
| `Throne-<version>-debian-arm64-system-qt.deb` | glibc 2.34 и системный Qt 6 | Raspberry Pi OS bookworm, Debian 12, Ubuntu 22.04 и новее на arm64 |

Каждый пакет зависит от той версии glibc, которая нужна его файлам, поэтому менеджер пакетов отказывается устанавливать слишком новый для системы пакет, а не устанавливает Throne, который не сможет запуститься. На Raspberry Pi используйте 64-битную Raspberry Pi OS. В bookworm и новее работают оба пакета arm64. В bullseye нет Qt 6, поэтому там используйте обычный пакет arm64.

Пакеты без `system-qt` содержат собственный Qt, но всё равно зависят от системных библиотек графики, шрифтов и X11, потому что одна и та же программа Throne содержит и приложение для компьютера. Менеджер пакетов устанавливает эти библиотеки. Дисплей не нужен.

Установите пакет и запустите службу:

```bash
sudo apt install ./Throne-<version>-debian-amd64.deb   # или: sudo dnf install ./Throne-<version>-fedora-amd64.rpm
sudo /opt/Throne/Throne --check                        # необязательно: может ли этот компьютер его запустить?
sudo systemctl enable --now throne
```

- Пакет никогда сам не включает и не запускает юнит. Обновление перезапускает юнит, если он работает, а удаление пакета останавливает и отключает его.
- `/var/lib/throne` (профили, настройки и ключ API) остаётся после удаления пакета, даже при `apt purge`. Чтобы начать заново, удалите его сами.
- `systemctl start throne` возвращает управление, когда Throne запустился. См. [systemd: готовность, статус и watchdog](@/headless/monitoring.ru.md#systemd).
- В `/etc/throne/throne.env` есть закомментированный пример для каждой переменной, которую может использовать юнит. Раскомментируйте строку и выполните `sudo systemctl restart throne`. Обновления сохраняют ваши изменения. Никогда не записывайте в этот файл ключ API; вместо этого укажите файл ключа в `THRONE_API_KEY_FILE`.
- Логи: `journalctl -u throne -f`.
- Пакеты также устанавливают `thronectl` как `/usr/bin/thronectl` и второй юнит, `throne-web.service`, для [веб-интерфейса](@/headless/web.ru.md#systemd). Он тоже не включается.

Чтобы изменить командную строку, добавьте drop-in-файл командой `sudo systemctl edit throne`:

```ini
[Service]
ExecStart=
ExecStart=/opt/Throne/Throne --headless --data-dir=%S/throne --debug
```

Юнит запускает Throne от root. В разделе [Права в Linux](#linux-privileges) этот способ сравнивается с другими способами запуска.

## Другие способы установки в Linux {#other-linux}

[Скрипт установки](@/get_started/installation.ru.md#linux-script) устанавливает Throne в `/opt/Throne`, а [портативный ZIP](@/get_started/installation.ru.md#linux-zip) лежит там, куда вы его распаковали. Ни один из них не устанавливает юнит. Установите юнит сами:

```bash
curl -fsSLo throne.service https://raw.githubusercontent.com/throneproj/Throne/dev/dist/linux/throne.service
sudo install -m 644 throne.service /etc/systemd/system/throne.service
sudo systemctl daemon-reload
sudo systemctl enable --now throne
```

Юнит запускает `/opt/Throne/Throne`. Если Throne находится в другой папке, измените этот путь drop-in-файлом, как показано ниже для пакета AUR. Для этого юнита файл `/etc/throne/throne.env` необязателен. Чтобы получить примеры, сохраните туда [`dist/linux/throne.env`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne.env).

[Пакет AUR](@/get_started/installation.ru.md#aur) поддерживается отдельно и устанавливает Throne в `/usr/lib/throne`. Если в нём нет юнита, установите юнит, описанный выше, и укажите в нём этот путь с помощью drop-in-файла (`sudo systemctl edit throne`):

```ini
[Service]
ExecStart=
ExecStart=/usr/lib/throne/Throne --headless --data-dir=%S/throne
```

## Права в Linux {#linux-privileges}

TUN и [kill switch](@/advanced/kill_switch.ru.md) требуют прав. Профилям с [дополнительным ядром (Extra Core)](@/advanced/chains.ru.md#extra-core) нужен обычный пользователь, от имени которого запускается это ядро, потому что Throne никогда не запускает дополнительное ядро от root. Выберите, как будет работать юнит:

| Как работает юнит | TUN | Kill switch | Профили Extra Core | Подходит для |
| --- | --- | --- | --- | --- |
| От root (юнит в поставляемом виде) | Да | Да | Нет | Шлюзов и выделенных устройств |
| От `throne` с возможностями (`caps.conf`) | Да | Нет (`unsupported`) | Да | Прокси-устройства без kill switch |
| От `throne` с `ThroneCore`, у которого setuid-root | Да | Да | Да | Всего, без запуска самого Throne от root |
| От `throne` без дополнительных настроек | Нет | Нет | Да | Только прокси (смешанный порт) |

**Возможности (capabilities).** Скачайте [`caps.conf`](https://github.com/throneproj/Throne/blob/dev/dist/linux/caps.conf), затем:

```bash
sudo useradd --system --no-create-home --home-dir /var/lib/throne --shell /usr/sbin/nologin throne
sudo install -Dm644 caps.conf /etc/systemd/system/throne.service.d/caps.conf
sudo systemctl daemon-reload && sudo systemctl restart throne
```

При следующем запуске systemd передаёт `/var/lib/throne` пользователю `throne`. В этой конфигурации у `ThroneCore` не должно быть ни бита setuid, ни файловых возможностей, потому что запуск такого файла сбрасывает возможности, которые выдаёт юнит.

**Ядро с setuid.** Используйте drop-in-файл, в котором в разделе `[Service]` есть только `User=throne` и `Group=throne`, и сделайте так, чтобы ядро запускалось от root:

```bash
sudo chown root:root /opt/Throne/ThroneCore && sudo chmod u+s /opt/Throne/ThroneCore
```

Обновление пакета заменяет `ThroneCore` файлом без бита setuid. В Debian и Ubuntu эта команда сохраняет бит при обновлениях:

```bash
sudo dpkg-statoverride --update --add root root 4755 /opt/Throne/ThroneCore
```

Не добавляйте в юнит `NoNewPrivileges=` или параметр усиления защиты, который его подразумевает: он помешает работе ядра с setuid.

`thronectl status` показывает, что может этот Throne, в `capabilities.tun` и `capabilities.kill_switch`. Ядро само проверяет, может ли оно открыть `/dev/net/tun` и есть ли у него `CAP_NET_ADMIN`, поэтому контейнер, работающий от root без `NET_ADMIN`, сообщает `false`, а не завершается ошибкой позже.

## Пользовательский юнит systemd {#user-unit}

Без root Throne работает как пользовательский юнит в вашем собственном сеансе. Скачайте [`throne-user.service`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne-user.service), затем:

```bash
install -Dm644 throne-user.service ~/.config/systemd/user/throne.service
systemctl --user daemon-reload && systemctl --user enable --now throne
loginctl enable-linger "$USER"   # запуск при загрузке, без входа в систему
```

- Юнит использует каталог данных приложения для компьютера, `~/.config/Throne`, поэтому у него те же профили и настройки. Сначала закройте приложение для компьютера. Пока юнит работает, приложение для компьютера сообщает, что Throne работает в фоне, и завершается. Перед тем как открыть приложение для компьютера, выполните `systemctl --user stop throne`.
- Для TUN и kill switch нужно ядро с setuid-root. Приложение для компьютера настраивает его, когда впервые запрашивает права (см. [Права](@/guides/tun_mode.ru.md#privileges-linux)), а `Throne --check` выводит нужные команды.
- `Throne --health`, `Throne --print-connection` и `Throne --check` находят этот Throne без параметров, если запускать их от имени вашего пользователя.

## Docker {#docker}

Образ `ghcr.io/throneproj/throne:<version>` (а также `latest`) собирается для `linux/amd64` и `linux/arm64`. Он содержит Throne в `/opt/Throne`, `thronectl` в `/usr/local/bin` и `tini` в качестве init. Он хранит данные в томе `/var/lib/throne`, для перезапуска завершается с кодом 75 и использует `Throne --health` как проверку состояния.

Начните с файла [`compose.yaml`](https://github.com/throneproj/Throne/blob/dev/docker/compose.yaml) из репозитория Throne. В нём используется тег `latest`; укажите там тег релиза, чтобы обновляться по собственному графику.

```bash
curl -fsSLO https://raw.githubusercontent.com/throneproj/Throne/dev/docker/compose.yaml
docker compose up -d
docker compose exec throne thronectl --key-file /var/lib/throne/config/api.key status
```

Важные части этого файла:

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

Примечания:

- **Порт прокси.** Смешанный порт по умолчанию слушает `127.0.0.1`, а опубликованный порт до него не доходит. Выполните `thronectl settings set inbound_address=0.0.0.0` и снова запустите профиль. Опубликованные порты Docker обходят брандмауэры хоста, например `ufw`: на хосте, доступном из интернета, публикуйте порт только на адресе в локальной сети, например `"192.168.1.2:2080:2080"`.
- **TUN и kill switch** требуют `cap_add: [NET_ADMIN]` и устройства `/dev/net/tun`. Без них контейнер всё равно обслуживает смешанный порт.
- **Сеть хоста.** С `network_mode: host` (и без `ports:`) TUN и kill switch действуют на весь хост, и только один Throne на хосте может использовать TUN. См. [Работа рядом с Throne для компьютера](@/headless/_index.ru.md#beside-desktop).
- **Остановка.** Оставьте `stop_grace_period: 45s`. Стандартные для Docker 10 секунд могут закончиться принудительным завершением Throne до того, как он остановит ядро. С `restart: unless-stopped` Docker снова запускает Throne после каждого завершения, даже после `thronectl shutdown`; останавливайте его командой `docker compose stop`.
- **Состояние.** Docker только помечает контейнер как нездоровый (unhealthy). Он его не перезапускает.
- **Ограничения.** Профили Extra Core не работают, потому что контейнер работает от root. В Docker без root (rootless) и других контейнерах с пространством имён пользователей kill switch завершается ошибкой `unsupported` (см. [Как Throne остаётся доступным](@/headless/recovery.ru.md#protection)).
- **Параметры.** Задавайте переменные в `environment:` или добавляйте флаги через `command:` — Docker добавляет их после `--headless`. Меняйте каталог данных только через `THRONE_DATA_DIR`, потому что проверка состояния находит Throne по этой переменной.
- **thronectl без `--key-file`.** Добавьте `THRONE_KEY_FILE: /var/lib/throne/config/api.key` в `environment:`, и `docker compose exec throne thronectl status` будет работать без дополнительных параметров. Сам Throne эту переменную игнорирует.
- **Импорт файла.** Передайте его через конвейер с `-T`: `docker compose exec -T throne thronectl import - < links.txt`.
- **API извне контейнера.** API слушает loopback контейнера. Чтобы обращаться к нему из сети, задайте `THRONE_API_LISTEN: "*:9095"` вместе со списком `THRONE_API_ALLOW` и опубликуйте порт 9095. Тогда административным запросам, например импорту и изменению настроек, нужен ещё и `THRONE_API_ADMIN_LAN: "1"`, потому что каждый запрос через опубликованный порт приходит извне контейнера. За NAT Docker у всех клиентов один и тот же адрес, поэтому один клиент со старым ключом блокирует остальных на минуту.
- **Логи:** `docker compose logs -f throne`.
- **Веб-интерфейс.** Задайте `THRONE_WEB_LISTEN: "*:9096"` и опубликуйте порт 9096, и контейнер будет также предоставлять [веб-интерфейс](@/headless/web.ru.md#docker).

## macOS {#macos}

LaunchDaemon запускает Throne от root при загрузке. TUN и kill switch работают, профили Extra Core — нет. Сначала переместите `Throne.app` в `/Applications`, скачайте [`moe.Throne.headless.plist`](https://github.com/throneproj/Throne/blob/dev/dist/macos/moe.Throne.headless.plist), затем:

```bash
sudo mkdir -p /Library/Logs/Throne
sudo install -m 644 -o root -g wheel moe.Throne.headless.plist /Library/LaunchDaemons/
sudo xattr -dr com.apple.quarantine /Applications/Throne.app   # только для неподписанных сборок
sudo launchctl bootstrap system /Library/LaunchDaemons/moe.Throne.headless.plist
```

- Чтобы остановить и выгрузить его: `sudo launchctl bootout system/moe.Throne.headless`.
- Его данные находятся в `/Library/Application Support/Throne`, а вывод — в `/Library/Logs/Throne/throne.log`.
- plist запускает исполняемый файл напрямую, поэтому launchd следит за процессом. Никогда не запускайте headless Throne через `open`: эта команда передаёт его LaunchServices и сразу возвращает управление.
- launchd снова запускает Throne через 10 секунд после завершения с ошибкой. Он не умеет исключать отдельные коды завершения, поэтому каждые 10 секунд он повторяет и те завершения, которые новый запуск не исправит: другой Throne использует каталог данных (код 3), неверный параметр (код 64) и ошибка конфигурации (код 78). Если Throne постоянно запускается и завершается, прочитайте лог.
- Чтобы узнать его состояние: `sudo /Applications/Throne.app/Contents/MacOS/Throne --health`.

## Служба Windows {#windows}

`Throne.exe --headless --service` работает как нативная служба Windows под учётной записью LocalSystem, поэтому TUN и kill switch работают.

1. Установите Throne для всех пользователей с помощью установщика в `Program Files`. Служба не должна запускать программу, которую может заменить обычный пользователь, поэтому не используйте установку для одного пользователя или ZIP в папке пользователя.
2. Скачайте [`install-service.bat`](https://github.com/throneproj/Throne/blob/dev/dist/windows/install-service.bat) и [`uninstall-service.bat`](https://github.com/throneproj/Throne/blob/dev/dist/windows/uninstall-service.bat).
3. В командной строке с правами администратора выполните:

   ```bat
   install-service.bat
   ```

   Если Throne находится в другой папке, укажите путь: `install-service.bat "D:\Apps\Throne\Throne.exe"`.

Скрипт создаёт службу `Throne`, запускает её после загрузки (отложенный запуск) и задаёт для неё действия восстановления:

```bat
sc.exe failure Throne reset= 86400 actions= restart/3000/restart/10000/restart/60000
sc.exe failureflag Throne 1
```

- `failureflag 1` заставляет Windows применять действия восстановления, когда Throne завершается с кодом ошибки, а не только после сбоя. Именно так запросы перезапуска (код 75) и завершения по watchdog снова запускают Throne. Каждое такое завершение считается сбоем: в течение суток первый перезапуск происходит через 3 секунды, второй — через 10, а последующие — через 60.
- `sc.exe stop Throne` и `thronectl shutdown` останавливают Throne окончательно (код 0). Когда Windows завершает работу, она даёт Throne до 45 секунд.
- Если служба остановилась с ошибкой, `sc.exe query Throne` показывает `WIN32_EXIT_CODE : 1066`, а собственный код завершения Throne — как `SERVICE_EXIT_CODE`.
- Её данные находятся в `C:\ProgramData\Throne`, а лог — в `C:\ProgramData\Throne\config\logs\throne.log`.
- `uninstall-service.bat` останавливает и удаляет службу. Данные в `C:\ProgramData\Throne` остаются.
- Перед обновлением Throne останавливайте службу.

Задача в планировщике заданий не заменяет службу: Windows останавливает задачу принудительно, поэтому Throne не может корректно остановить своё ядро, и не умеет перезапускать задачу в зависимости от кода завершения.

### Папка данных {#windows-data-folder}

Служба оставляет доступ к `C:\ProgramData\Throne` только SYSTEM и администраторам (Administrators). Она создаёт папку с такими правами и снова задаёт эти права при каждом запуске, в том числе для файлов в ней. Если папка уже существует, но принадлежит другому пользователю, позволяет другим пользователям записывать в неё (так ведёт себя папка, созданная вручную в `C:\ProgramData`), является ссылкой или junction-точкой или содержит такой файл или папку, служба завершается с кодом 78 и называет проблему в логе. Удалите эту папку и снова запустите службу. Для `--data-dir`, в который могут записывать обычные пользователи, в лог записывается только предупреждение.

### Команды из командной строки {#windows-prompt}

Выполняйте `Throne.exe --health`, `--print-connection`, `--check` и `thronectl` из командной строки с правами администратора, а командам `Throne.exe` передавайте `--data-dir C:\ProgramData\Throne`. Администратор — это не LocalSystem, поэтому каталог данных по умолчанию у него другой. Открыть канал (pipe) службы и прочитать её файл ключа могут только администраторы и SYSTEM. Из командной строки без повышенных прав `--health` записывает в лог `[Instance] Not talking to … access denied` и завершается с кодом 2.

`Throne.exe` — программа Windows с графическим интерфейсом, поэтому командная строка не ждёт её завершения. Throne выводит данные в консоль программы, которая его запустила. Чтобы дождаться его завершения и прочитать код завершения:

```bat
start "" /wait "C:\Program Files\Throne\Throne.exe" --health --data-dir C:\ProgramData\Throne
echo %ERRORLEVEL%
```

```powershell
$p = Start-Process 'C:\Program Files\Throne\Throne.exe' -ArgumentList '--health', '--data-dir', 'C:\ProgramData\Throne' -Wait -PassThru
$p.ExitCode
```

`--print-connection` выводит строки `export` в формате POSIX. В PowerShell задайте значения сами:

```powershell
$env:THRONE_ADDR = '127.0.0.1:9095'
$env:THRONE_KEY_FILE = 'C:\ProgramData\Throne\config\api.key'
thronectl status
```

### Запуск из консоли {#windows-console}

Чтобы проверить headless Throne без службы:

```bat
start "" /wait "C:\Program Files\Throne\Throne.exe" --headless
```

`Ctrl+C` корректно останавливает его, а повторное нажатие `Ctrl+C` сразу принудительно завершает ядро. Закрытие окна консоли, `taskkill /PID <pid>` без `/F`, выход из системы и завершение работы Windows тоже корректно его останавливают. Вне службы каталог данных — `%LOCALAPPDATA%\Throne` (`C:\ProgramData\Throne`, если Throne работает от LocalSystem). При перезапуске Throne запускает новый процесс и завершается с кодом 0, поэтому `start /wait` возвращает управление, а новый процесс продолжает работать.

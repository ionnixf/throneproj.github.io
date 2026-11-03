+++
title = "Throne в режиме headless"
description = "Как запускать Throne без окна на сервере, роутере, Raspberry Pi или в контейнере и управлять им через thronectl."
weight = 4
sort_by = "weight"
toc = true
+++

`Throne --headless` запускает Throne без окна: на сервере, роутере, Raspberry Pi, NAS или в контейнере. Это та же программа, что и приложение для компьютера, с тем же ядром, профилями, профилями маршрутизации и настройками, но она не показывает ни окна, ни значка в трее, ни диалогов. Вы управляете ею из терминала с помощью `thronectl` через [Throne API](@/advanced/api.ru.md), который headless Throne включает при первом запуске.

Headless Throne доступен начиная с Throne 1.4.0 в Linux, macOS и Windows. В Throne для Android режима headless нет.

Типичные сценарии:

- **Прокси-сервер.** Throne запускает профиль, а другие устройства в сети пользуются его смешанным портом (HTTP и SOCKS). См. [Общий доступ к прокси для других устройств](@/guides/proxy_modes.ru.md#lan-sharing).
- **Шлюз.** Throne работает в режиме TUN на устройстве, которое маршрутизирует трафик сети.
- **Контейнер.** Образ Docker запускает Throne, а его данные хранятся в томе.
- **Ваш собственный компьютер, без окна.** Пользовательский юнит systemd сохраняет подключение ваших профилей из приложения для компьютера после того, как вы выйдете из системы.

Страницы этого раздела:

- [Установка и службы](@/headless/installation.ru.md): пакеты для Linux, Docker, macOS и служба Windows.
- [Управление через thronectl](@/headless/thronectl.ru.md): подключение `thronectl`, добавление подписок, перенос конфигурации из приложения для компьютера, изменение настроек.
- [Веб-интерфейс](@/headless/web.ru.md): управление Throne из браузера с помощью `thronectl web`.
- [Командная строка и каталог данных](@/headless/command_line.ru.md): все параметры и переменные, где хранятся данные, перезапуски и коды завершения.
- [Проверки состояния и логи](@/headless/monitoring.ru.md): `Throne --check`, `Throne --health`, статус и логи systemd.
- [Защита от потери доступа и восстановление](@/headless/recovery.ru.md): как Throne сохраняет доступ к API и что делать, если настройки лишили вас доступа.
- [Устранение неполадок](@/headless/troubleshooting.ru.md): типичные проблемы и их решения.

## Быстрый старт {#quick-start}

В Debian 10, Ubuntu 20.04 или новее (amd64):

1. Скачайте `Throne-<version>-debian-amd64.deb` со [страницы релизов](https://github.com/throneproj/Throne/releases). В Debian 12, Ubuntu 22.04 или новее вместо этого можно подключить [репозиторий APT](@/get_started/installation.ru.md#apt-repository).
2. Установите пакет и запустите службу:

   ```bash
   sudo apt install ./Throne-<version>-debian-amd64.deb
   sudo systemctl enable --now throne
   systemctl status throne
   ```

   `systemctl status` показывает, чем занят Throne, например `Status: "Idle"`.

3. Throne хранит данные в `/var/lib/throne`, а ключ API — в `/var/lib/throne/config/api.key`, который может читать только root. Скопируйте ключ в собственный файл:

   ```bash
   mkdir -p ~/.config/throne
   sudo install -m 600 -o "$USER" /var/lib/throne/config/api.key ~/.config/throne/api-key
   export THRONE_KEY_FILE=~/.config/throne/api-key
   ```

4. Пакет также устанавливает `thronectl` — клиент командной строки — как `/usr/bin/thronectl`. Чтобы управлять Throne с другого компьютера, см. [Установка thronectl](@/headless/thronectl.ru.md#install-thronectl).
5. Добавьте подписку и подключитесь:

   ```bash
   thronectl status
   thronectl sub add 'https://sub.example.com/abc' --name Provider
   thronectl profiles
   thronectl connect 12
   ```

   Замените `12` на ID профиля из списка `thronectl profiles` или используйте `thronectl connect best`, чтобы выбрать профиль с наименьшей задержкой.

Теперь в Throne работает профиль, и Throne снова запускает его после каждой перезагрузки, пока вы не выполните `thronectl disconnect`. Чтобы перед запуском проверить, может ли устройство вообще запустить Throne, выполните `sudo /opt/Throne/Throne --check`. См. [Throne --check](@/headless/monitoring.ru.md#check).

## Как работает headless Throne {#how-it-works}

Throne никогда не переходит в фоновый режим сам. Его запускает при загрузке, перезапускает после сбоя и останавливает менеджер служб: systemd в Linux, launchd в macOS, диспетчер управления службами (Service Control Manager) в Windows или Docker. В репозитории Throne есть готовый файл для каждого из них:

| Файл | Что это |
| --- | --- |
| [`dist/linux/throne.service`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne.service) | Системный юнит systemd, работает от root. Пакеты `.deb` и `.rpm` устанавливают его как `/usr/lib/systemd/system/throne.service`. |
| [`dist/linux/throne.env`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne.env) | Файл окружения для этого юнита, устанавливается как `/etc/throne/throne.env`. Каждая переменная в нём — закомментированный пример. |
| [`dist/linux/caps.conf`](https://github.com/throneproj/Throne/blob/dev/dist/linux/caps.conf) | Необязательный drop-in-файл: запускать юнит от имени пользователя `throne` с возможностями (capabilities) вместо root. |
| [`dist/linux/throne-user.service`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne-user.service) | Пользовательский юнит systemd, без root. |
| [`dist/linux/throne-web.service`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne-web.service), [`throne-web.env`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne-web.env) | Юнит systemd для [веб-интерфейса](@/headless/web.ru.md#systemd), который запускает `thronectl web` от собственного пользователя, и его файл окружения. Пакеты устанавливают их как `/usr/lib/systemd/system/throne-web.service` и `/etc/throne/web.env`. |
| [`dist/macos/moe.Throne.headless.plist`](https://github.com/throneproj/Throne/blob/dev/dist/macos/moe.Throne.headless.plist) | LaunchDaemon для launchd. |
| [`dist/windows/install-service.bat`](https://github.com/throneproj/Throne/blob/dev/dist/windows/install-service.bat), [`uninstall-service.bat`](https://github.com/throneproj/Throne/blob/dev/dist/windows/uninstall-service.bat) | Установка и удаление службы Windows. |
| [`docker/compose.yaml`](https://github.com/throneproj/Throne/blob/dev/docker/compose.yaml) | Пример Docker Compose для образа `ghcr.io/throneproj/throne`. |

Что происходит, когда работает headless Throne:

- Он хранит профили, группы, профили маршрутизации и настройки в [каталоге данных](@/headless/command_line.ru.md#data-dir), в той же базе данных `throne.db`, что и приложение для компьютера.
- Throne API слушает `127.0.0.1:9095` с первого запуска, с ключом, который Throne генерирует в файл `config/api.key` в каталоге данных. `thronectl` использует этот ключ. См. [Подключение thronectl к Throne](@/headless/thronectl.ru.md#connect).
- Он запоминает запущенный профиль и то, был ли включён TUN, и снова запускает их при каждом запуске, пока вы не отключитесь. `--no-resume` отключает это на один запуск.
- Он сообщает своё состояние менеджеру служб и сам следит за ходом своей работы. Если ядро постоянно падает, профиль зависает или главный цикл останавливается, Throne завершается с кодом, по которому менеджер служб запускает его снова. См. [Коды завершения](@/headless/command_line.ru.md#exit-codes).
- `Throne --health` спрашивает работающий Throne о его состоянии, а `Throne --check` сообщает, может ли компьютер его запустить. См. [Проверки состояния и логи](@/headless/monitoring.ru.md).

## Отличия от приложения для компьютера {#differences}

Headless Throne ничего не спрашивает. То, о чём приложение для компьютера спрашивает в диалоге, headless Throne решает сам или записывает в лог:

| Ситуация | Headless Throne |
| --- | --- |
| Уведомление или ошибка | Записывает это в лог. |
| TUN нужны права | Отказывает с `needs_privileges`. `Throne --check` сообщает, чего не хватает. |
| Профилю Xray нужны файлы geoip или geosite | Загружает их и пробует запустить профиль ещё раз. |
| [Kill switch](@/advanced/kill_switch.ru.md) не может работать | Записывает в лог причину, сообщает `kill_switch_failed` и отказывается запускать профили. |
| VPN-профиль запрашивает одноразовый код | Отвечает с помощью [записи OTP](@/advanced/vpn_profiles.ru.md#otp-manager) профиля, если она есть. |
| VPN-профиль запрашивает что-то другое, например вход через веб | Отменяет запрос, когда истекает его срок, — не позже чем через 5 минут (`vpn_challenge_pending`). Если сервер отклоняет сохранённые учётные данные, это даёт `vpn_auth_failed`. |
| Настройке нужен перезапуск программы | Записывает это в лог. Настройка применится после следующего перезапуска (`thronectl restart`). |
| Условия использования WARP | Никогда их не принимает. |

Эти функции требуют окна и недоступны в headless Throne:

- Системный прокси (`thronectl proxy on` завершается ошибкой `system_proxy_unsupported`), значок в трее, глобальные горячие клавиши и редакторы.
- Встроенное обновление, а также перезапуск с повышенными правами или с TUN. Обновляйте Throne через пакет, образ Docker или установщик.
- [Глубокие ссылки](@/advanced/deeplinks.ru.md) `throne://` и ассоциации файлов. Вместо этого импортируйте через `thronectl import`.
- Запросы входа у VPN-профилей, например SSO, кроме одноразовых кодов из записи OTP.
- Регистрация WARP.
- Настройки, которыми API не управляет, и восстановление профилей дополнительного ядра (Extra Core) через API.

Настройте это в Throne для компьютера и перенесите с помощью [резервной копии](@/headless/thronectl.ru.md#from-desktop). Если вы используете пользовательский юнит systemd, остановите юнит и откройте приложение для компьютера: оба используют одни и те же данные.

## Работа рядом с Throne для компьютера {#beside-desktop}

Интерфейс TUN (`throne-tun`), DNS-порт ядра (`dns_in_port`, по умолчанию 5533) и маршруты TUN принадлежат всему компьютеру, поэтому TUN на нём может одновременно использовать только один Throne. Headless Throne включает TUN, только если:

- нет другого интерфейса `throne-tun` (проверяется в Linux и Windows),
- ни один сетевой интерфейс не занимает адрес TUN (`tun_ipv4_cidr`, по умолчанию `172.19.0.1/24`) и
- DNS-порт свободен, пока ни один профиль не запущен.

Иначе `thronectl tun on` завершается ошибкой `tun_conflict`, а `thronectl status` показывает, что занимает TUN. Если TUN не удаётся снова включить при запуске, `Throne --health` сообщает `tun_unavailable`. Если мост Docker или другая сеть использует диапазон TUN Throne, перенесите TUN в свободный диапазон, а затем снова включите его:

```bash
thronectl settings set tun_ipv4_cidr=172.29.0.1/24
thronectl tun on
```

Если DNS-порт занят другой программой, укажите Throne свободный порт командой `thronectl settings set dns_in_port=PORT`.

Headless Throne также отмечает, что он работает: в `/run/throne` от root (`/var/run/throne` в macOS), в `$XDG_RUNTIME_DIR/throne` от имени пользователя, а в Windows — как `Global\ThroneHeadless` (`Local\ThroneHeadless`, если он работает без повышенных прав и не как служба; такую отметку видно только в том же сеансе Windows). Тогда Throne для компьютера на том же компьютере:

- при запуске предупреждает, что работает headless Throne,
- отказывается включать TUN, пока работает headless Throne, и
- не обновляется сам, пока headless Throne работает из той же установки.

Два Throne никогда не используют один каталог данных. Если пользовательский юнит headless использует каталог данных приложения для компьютера, приложение для компьютера сообщает, что Throne работает в фоне, и завершается. Сначала остановите юнит командой `systemctl --user stop throne`. Второй headless Throne с собственным каталогом данных может работать рядом с первым; описанные выше проверки не дают им одновременно использовать TUN.

## Идентификатор устройства {#device-id}

Некоторые подписки запрашивают идентификатор устройства (заголовок `x-hwid`, см. [User-Agent и HWID](@/guides/subscriptions.ru.md#user-agent-and-hwid)). В Linux Throne берёт его из `/etc/machine-id`, а если этого файла нет — из `/var/lib/dbus/machine-id`. Система без обоих файлов, например контейнер, при первом запуске получает сгенерированный идентификатор в `config/hwid`. Этот идентификатор остаётся с каталогом данных.

Официальный образ Docker поставляется без machine ID, поэтому каждый контейнер получает собственный идентификатор. Если вы собираете свой образ, не кладите в него файл `machine-id`: иначе каждый контейнер из этого образа будет отправлять один и тот же идентификатор.

macOS использует аппаратный UUID, а Windows — `MachineGuid` из реестра.

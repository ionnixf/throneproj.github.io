+++
title = "Throne в режиме headless"
description = "Как запускать Throne без окна на сервере, роутере, Raspberry Pi или в контейнере и управлять им через thronectl."
weight = 4
sort_by = "weight"
toc = true
+++

`Throne --headless` запускает Throne без окна: на сервере, роутере, Raspberry Pi или в контейнере. Это та же программа, что и приложение для компьютера, с тем же ядром, профилями и настройками. Вы управляете ею с помощью `thronectl` через [Throne API](@/advanced/api.ru.md), который включён с первого запуска. Нужен Throne 1.4.0 или новее в Linux, macOS или Windows. В Throne для Android режима headless нет.

Типичные сценарии: прокси-сервер, смешанным портом которого [пользуются другие устройства](@/guides/proxy_modes.ru.md#lan-sharing), шлюз с TUN для сети, контейнер Docker или [пользовательский юнит systemd](@/headless/installation.ru.md#user-unit), который сохраняет подключение ваших профилей из приложения для компьютера после выхода из системы.

## Быстрый старт {#quick-start}

В Debian 10, Ubuntu 20.04 или новее (amd64):

1. Скачайте `Throne-<version>-debian-amd64.deb` со [страницы релизов](https://github.com/throneproj/Throne/releases) или подключите [репозиторий APT](@/get_started/installation.ru.md#apt-repository) (Debian 12, Ubuntu 22.04 или новее).
2. Установите пакет и запустите службу:

   ```bash
   sudo apt install ./Throne-<version>-debian-amd64.deb
   sudo systemctl enable --now throne
   ```

3. Скопируйте ключ API, который может читать только root, в собственный файл:

   ```bash
   mkdir -p ~/.config/throne
   sudo install -m 600 -o "$USER" /var/lib/throne/config/api.key ~/.config/throne/api-key
   export THRONE_KEY_FILE=~/.config/throne/api-key
   ```

4. Добавьте подписку и подключитесь с помощью `thronectl`, который устанавливает пакет:

   ```bash
   thronectl status
   thronectl sub add 'https://sub.example.com/abc' --name Provider
   thronectl profiles
   thronectl connect 12
   ```

   Замените `12` на ID профиля из списка или используйте `thronectl connect best`, чтобы выбрать профиль с наименьшей задержкой.

Throne снова запускает этот профиль после каждой перезагрузки, пока вы не выполните `thronectl disconnect`. Если что-то не работает, выполните `sudo /opt/Throne/Throne --check` (см. [Throne --check](@/headless/monitoring.ru.md#check)). Docker, macOS, Windows и другие варианты установки в Linux описаны в разделе [Установка и службы](@/headless/installation.ru.md).

## Как работает headless Throne {#how-it-works}

- Его запускает, перезапускает и останавливает менеджер служб: systemd, launchd, диспетчер управления службами Windows или Docker. Throne никогда не переходит в фоновый режим сам.
- Он хранит профили, группы, профили маршрутизации и настройки в [каталоге данных](@/headless/command_line.ru.md#data-dir), в той же базе `throne.db`, что и приложение для компьютера.
- API слушает `127.0.0.1:9095` с ключом, который Throne создаёт в файле `config/api.key` в каталоге данных. См. [Подключение thronectl к Throne](@/headless/thronectl.ru.md#connect).
- При каждом запуске он снова запускает последний профиль, с TUN, если TUN был включён. `--no-resume` пропускает это один раз.
- Если ядро постоянно падает или профиль зависает, Throne завершается с кодом, по которому менеджер служб запускает его снова. См. [Коды завершения](@/headless/command_line.ru.md#exit-codes).

## Отличия от приложения для компьютера {#differences}

Headless Throne ничего не спрашивает. Он решает сам или записывает в лог:

- Включение TUN без нужных прав завершается ошибкой `needs_privileges`. `Throne --check` сообщает, чего не хватает.
- Если [kill switch](@/advanced/kill_switch.ru.md) не может работать, Throne сообщает `kill_switch_failed` и отказывается запускать профили.
- VPN-профиль, который запрашивает одноразовый код, получает его из своей [записи OTP](@/advanced/vpn_profiles.ru.md#otp-manager). Любой другой запрос, например вход через веб, отменяется, когда истекает его срок, — не позже чем через 5 минут (`vpn_challenge_pending`). Если сервер отклоняет сохранённые учётные данные, это даёт `vpn_auth_failed`.
- Настройка, которой нужен перезапуск программы, применяется после `thronectl restart`.

Системного прокси (`thronectl proxy on` завершается ошибкой `system_proxy_unsupported`), значка в трее, глобальных горячих клавиш и встроенного обновления нет: обновляйте Throne через пакет, образ Docker или установщик. [Глубокие ссылки](@/advanced/deeplinks.ru.md) `throne://` и ассоциации файлов не работают; импортируйте через `thronectl import`.

Записи OTP, учётные данные VPN и настройки, которыми API не управляет, настройте в Throne для компьютера и перенесите с помощью [резервной копии](@/headless/thronectl.ru.md#from-desktop). Пользовательский юнит systemd использует те же данные, что и приложение для компьютера: остановите юнит и откройте приложение.

## Работа рядом с Throne для компьютера {#beside-desktop}

TUN на компьютере может одновременно использовать только один Throne, потому что интерфейс `throne-tun`, диапазон адресов TUN (`tun_ipv4_cidr`, по умолчанию `172.19.0.1/24`) и DNS-порт ядра (`dns_in_port`, по умолчанию 5533) принадлежат всему компьютеру. Если что-то из этого занято, `thronectl tun on` завершается ошибкой `tun_conflict`, а `thronectl status` показывает, что именно его занимает. Если мост Docker или другая сеть использует диапазон TUN, перенесите TUN в свободный диапазон; если DNS-порт занят другой программой, укажите Throne другой порт:

```bash
thronectl settings set tun_ipv4_cidr=172.29.0.1/24    # или: dns_in_port=PORT
thronectl tun on
```

Пока работает headless Throne, Throne для компьютера на том же компьютере предупреждает об этом при запуске, отказывается включать TUN и не обновляется сам, если оба работают из одной установки.

Два Throne никогда не используют один каталог данных. Если пользовательский юнит использует каталог данных приложения для компьютера, приложение сообщает, что Throne работает в фоне, и завершается. Сначала остановите юнит: `systemctl --user stop throne`.

## Идентификатор устройства {#device-id}

Некоторые подписки запрашивают идентификатор устройства (см. [User-Agent и HWID](@/guides/subscriptions.ru.md#user-agent-and-hwid)). В Linux Throne берёт его из `/etc/machine-id`, а если этого файла нет — из `/var/lib/dbus/machine-id`. Если нет обоих файлов, как в контейнере, при первом запуске он создаёт идентификатор в `config/hwid`, который остаётся с каталогом данных. macOS использует аппаратный UUID, а Windows — `MachineGuid` из реестра.

Официальный образ Docker поставляется без machine ID, поэтому каждый контейнер получает собственный идентификатор. Если вы собираете свой образ, не кладите в него файл `machine-id`: иначе каждый контейнер из этого образа будет отправлять один и тот же идентификатор.

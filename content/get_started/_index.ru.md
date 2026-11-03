+++
title = "Начало работы"
description = "Что такое Throne, в каких системах он работает и с чего начать."
weight = 1
sort_by = "weight"
aliases = ["/ru/introduction/"]
+++

В этом разделе рассказывается, что такое Throne, какие системы он поддерживает и какие страницы стоит прочитать в первую очередь. Если вы впервые пользуетесь Throne, начните отсюда.

## Что такое Throne {#what-is-throne}

Throne (ранее Nekoray) — бесплатный прокси-клиент с открытым исходным кодом (GPL-3.0) для Windows, Linux и macOS. У него графический интерфейс на Qt, а работает он на ядре ThroneCore: это ядро основано на sing-box и также запускает Xray для профилей, которым он нужен. Вы добавляете серверы своего провайдера или собственный сервер, выбираете, какой трафик пойдёт через них, и подключаетесь в режиме системного прокси или в режиме TUN.

Throne для Android (ранее NekoBox for Android) — это приложение для Android. Начиная с версии 2.0.0 оно использует то же ядро, ту же модель настроек и тот же формат резервных копий, что и приложение для компьютера, поэтому свою конфигурацию можно переносить между компьютером и телефоном. См. [Throne для Android](@/android/_index.ru.md).

Начиная с 1.4.0 та же программа Throne работает и без окна — на сервере, роутере или в контейнере, а управляют ею с помощью инструмента командной строки `thronectl`. См. [Headless Throne](@/headless/_index.ru.md).

Если раньше вы пользовались Nekoray или NekoBox, прочитайте страницу [Переход с Nekoray / NekoBox](@/help/migrating.ru.md).

## Платформы {#platforms}

| Система | Требования | Типы пакетов |
|---|---|---|
| Windows | Windows 10 1809 или новее (x64, ARM64). Windows 7 SP1 или новее — с legacy-сборками (x64, 32-битная x86). | Установщик, портативный ZIP |
| Linux | x64 или ARM64, glibc 2.28 или новее, например Debian 10, Ubuntu 20.04 или RHEL 8 (начиная с 1.4.0). Пакетам `-system-qt` нужна glibc 2.34 или новее. | Портативный ZIP, `.deb`, `.rpm`, установочный скрипт, [APT- и RPM-репозитории](@/get_started/installation.ru.md#apt-repository), [AUR](@/get_started/installation.ru.md#aur) |
| macOS | macOS 14.4 или новее (Apple Silicon и Intel). macOS 10.15 или новее — с legacy-сборкой (Intel). | ZIP, [Homebrew](@/get_started/installation.ru.md#homebrew) |
| Android | Android 7.0 или новее | APK |

Скачать файлы можно на [странице загрузок](@/downloads.ru.md).

## Основные возможности {#highlights}

- Множество протоколов, включая VLESS с REALITY и XHTTP, Shadowsocks, Trojan, Hysteria, TUIC, WireGuard и AmneziaWG, NaïveProxy, OpenVPN и OpenConnect: [Протоколы и форматы импорта](@/reference/protocols.ru.md).
- DNS-туннели, которые передают ваш трафик внутри DNS-запросов: DNSTT/VayDNS, MasterDNS и XDNS для VLESS (Xray) (начиная с 1.4.0): [DNS-туннели](@/advanced/dns_tunnels.ru.md).
- Подписки, которые обновляются по расписанию, с настройками User-Agent и HWID для каждой группы: [Подписки и группы](@/guides/subscriptions.ru.md).
- Режим системного прокси или режим TUN для всех приложений, а также раздача прокси другим устройствам в вашей сети: [Системный прокси, TUN и доступ из локальной сети](@/guides/proxy_modes.ru.md).
- Профили маршрутизации с правилами и наборами правил, а также готовые профили для Китая, Ирана и России: [Маршрутизация](@/guides/routing.ru.md).
- Тесты задержки, IP и скорости, а также автовыбор (Auto Selector), который удерживает вас на работающем сервере: [Тестирование и автовыбор](@/guides/testing.ru.md).
- Цепочки прокси и ваши собственные конфигурации sing-box или Xray: [Цепочки прокси и пользовательские конфигурации](@/advanced/chains.ru.md).
- Средства обхода цензуры, например фрагментация TLS, uTLS и ECH: [Пресеты для обхода цензуры](@/advanced/presets.ru.md).
- Встроенный Cloudflare WARP: [Cloudflare WARP](@/advanced/warp.ru.md).
- Резервные копии, которые работают и в приложении для компьютера, и в приложении для Android: [Резервное копирование, обновление и перенос](@/guides/backup.ru.md).
- Kill switch, который блокирует трафик в обход Throne (начиная с 1.4.0): [Kill Switch](@/advanced/kill_switch.ru.md).
- Throne API для управления и администрирования Throne из скриптов, с помощью горячих клавиш и с других устройств через `thronectl` (начиная с 1.4.0): [Throne API и thronectl](@/advanced/api.ru.md).
- Режим headless для серверов и контейнеров (начиная с 1.4.0): [Headless Throne](@/headless/_index.ru.md).
- Простой режим, который скрывает дополнительные параметры от новых пользователей, и поиск команд и параметров по `Ctrl+K` (начиная с 1.4.0): [Простой и расширенный режим](@/get_started/configuration.ru.md#interface-mode).

## С чего начать {#where-to-start}

1. Установите Throne: [Установка](@/get_started/installation.ru.md).
2. Добавьте свои серверы и подключитесь: [Быстрый старт](@/get_started/configuration.ru.md).
3. Прочитайте [Руководства](@/guides/_index.ru.md) по нужным вам функциям, например по подпискам, маршрутизации и режиму TUN.

На Android начните со страницы [Throne для Android](@/android/_index.ru.md). Если что-то не работает, см. [Устранение неполадок](@/help/troubleshooting.ru.md) и [FAQ](@/help/faq.ru.md).

## Благодарности {#credits}

Throne построен на основе этих проектов:

- [SagerNet/sing-box](https://github.com/SagerNet/sing-box): прокси-движок ядра
- [XTLS/Xray-core](https://github.com/xtls/xray-core): движок Xray
- [Qv2ray](https://github.com/Qv2ray/Qv2ray): части интерфейса и код системного прокси
- [Qt](https://www.qt.io/): инструментарий для интерфейса
- [simple-protobuf](https://github.com/tonda-kriz/simple-protobuf): обмен сообщениями между интерфейсом и ядром
- [fkYAML](https://github.com/fktn-k/fkYAML): импорт Clash YAML
- [quirc](https://github.com/dlbeer/quirc): чтение QR-кодов
- [srombauts/sqlitecpp](https://github.com/srombauts/sqlitecpp): база данных профилей и настроек

Чтобы поддержать разработку Throne, нажмите `Поддержать Throne` (Support Throne) в окне `Программа` (Program) → `О программе` (About) (начиная с 1.4.0). Кнопка открывает страницу с информацией о пожертвованиях. В Throne для Android такой же пункт есть на экране `О приложении` (About) (начиная с 2.0.4).

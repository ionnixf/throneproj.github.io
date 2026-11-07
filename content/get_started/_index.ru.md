+++
title = "Начало работы"
description = "Что такое Throne, в каких системах он работает и с чего начать."
weight = 1
sort_by = "weight"
aliases = ["/ru/introduction/"]
+++

## Что такое Throne {#what-is-throne}

Throne (ранее Nekoray) — бесплатный прокси-клиент с открытым исходным кодом (GPL-3.0) для Windows, Linux и macOS. Его ядро ThroneCore основано на sing-box и также запускает Xray для профилей, которым он нужен. Вы добавляете серверы, выбираете, какой трафик пойдёт через них, и подключаетесь в режиме системного прокси или в режиме TUN.

- [Throne для Android](@/android/_index.ru.md) (ранее NekoBox for Android) использует то же ядро, те же настройки и тот же [формат резервных копий](@/guides/backup.ru.md), поэтому свою конфигурацию можно переносить между компьютером и телефоном.
- [Headless Throne](@/headless/_index.ru.md) работает без окна — на сервере, роутере или в контейнере, а управляют им через `thronectl`.
- Если раньше вы пользовались Nekoray или NekoBox, прочитайте страницу [Переход с Nekoray / NekoBox](@/help/migrating.ru.md).

## Платформы {#platforms}

| Система | Требования | Типы пакетов |
|---|---|---|
| Windows | Windows 10 1809 или новее (x64, ARM64). Windows 7 SP1 или новее — с legacy-сборками (x64, 32-битная x86). | Установщик, портативный ZIP |
| Linux | x64 или ARM64, glibc 2.28 или новее, например Debian 10, Ubuntu 20.04 или RHEL 8. Пакетам `-system-qt` нужна glibc 2.34 или новее. | Портативный ZIP, `.deb`, `.rpm`, установочный скрипт, [APT- и RPM-репозитории](@/get_started/installation.ru.md#apt-repository), [AUR](@/get_started/installation.ru.md#aur) |
| macOS | macOS 14.4 или новее (Apple Silicon и Intel). macOS 10.15 или новее — с legacy-сборкой (Intel). | ZIP, [Homebrew](@/get_started/installation.ru.md#homebrew) |
| Android | Android 7.0 или новее | APK |

Скачать файлы можно на [странице загрузок](@/downloads.ru.md).

## Основные возможности {#highlights}

- Множество [протоколов](@/reference/protocols.ru.md), включая VLESS с REALITY и XHTTP, Shadowsocks, Trojan, Hysteria, TUIC, WireGuard, AmneziaWG, NaïveProxy, OpenVPN, OpenConnect и [DNS-туннели](@/advanced/dns_tunnels.ru.md).
- [Подписки](@/guides/subscriptions.ru.md), которые обновляются по расписанию.
- [Системный прокси, режим TUN и раздача прокси в локальной сети](@/guides/proxy_modes.ru.md).
- Правила [маршрутизации](@/guides/routing.ru.md) и готовые профили для Китая, Ирана и России.
- [Тесты](@/guides/testing.ru.md) задержки и скорости, а также автовыбор (Auto Selector), который удерживает вас на работающем сервере.
- [Цепочки прокси и пользовательские конфигурации](@/advanced/chains.ru.md), [средства обхода цензуры](@/advanced/presets.ru.md), например фрагментация TLS, и встроенный [Cloudflare WARP](@/advanced/warp.ru.md).
- [Kill switch](@/advanced/kill_switch.ru.md) и [управление через `thronectl`](@/advanced/api.ru.md) из скриптов и с других устройств.
- [Простой режим](@/get_started/configuration.ru.md#interface-mode) для новых пользователей и поиск команд и параметров по `Ctrl+K`.

## С чего начать {#where-to-start}

1. Установите Throne: [Установка](@/get_started/installation.ru.md).
2. Добавьте свои серверы и подключитесь: [Быстрый старт](@/get_started/configuration.ru.md).
3. Прочитайте [Руководства](@/guides/_index.ru.md) по нужным вам функциям.

На Android начните со страницы [Throne для Android](@/android/_index.ru.md). Если что-то не работает, см. [Устранение неполадок](@/help/troubleshooting.ru.md) и [FAQ](@/help/faq.ru.md).

## Благодарности {#credits}

Throne построен на основе [sing-box](https://github.com/SagerNet/sing-box), [Xray-core](https://github.com/xtls/xray-core), [Qv2ray](https://github.com/Qv2ray/Qv2ray), [Qt](https://www.qt.io/), [simple-protobuf](https://github.com/tonda-kriz/simple-protobuf), [fkYAML](https://github.com/fktn-k/fkYAML), [quirc](https://github.com/dlbeer/quirc) и [SQLiteCpp](https://github.com/srombauts/sqlitecpp).

Чтобы поддержать Throne, нажмите `Поддержать Throne` (Support Throne) в окне `Программа` (Program) → `О программе` (About). В Throne для Android этот пункт есть на экране `О приложении` (About).

+++
title = "Глоссарий"
description = "Краткие объяснения терминов, которые встречаются в Throne."
weight = 50
toc = true
+++

## Профили и группы {#profiles-and-groups}

| Термин | Значение | Подробнее |
| --- | --- | --- |
| Profile (профиль) | Одна запись сервера или прокси в списке с её протоколом, адресом и настройками. | [Быстрый старт](@/get_started/configuration.ru.md#add-servers) |
| Group (группа) | Вкладка главного окна, в которой хранятся профили. | [Подписки и группы](@/guides/subscriptions.ru.md#group-options) |
| Subscription (подписка) | URL от вашего провайдера, который возвращает список профилей; Throne загружает их в группу. | [Подписки и группы](@/guides/subscriptions.ru.md#add-subscription) |
| Share link (ссылка для обмена) | Однострочная ссылка на один профиль, например `vless://…`, которую понимают и другие приложения. | [Протоколы и форматы импорта](@/reference/protocols.ru.md#share-links) |
| Deep link (глубокая ссылка) | Ссылка `throne://`, которая даёт Throne команду выполнить действие, например добавить подписку. | [Глубокие ссылки](@/advanced/deeplinks.ru.md#format) |
| Throne link (ссылка Throne) | Глубокая ссылка `throne://add/…`, которая содержит один профиль любого типа. | [Глубокие ссылки](@/advanced/deeplinks.ru.md#add) |
| Chain (цепочка) | Профиль `Цепочка прокси` (Chain Proxy), который пропускает трафик через несколько профилей один за другим. | [Цепочки прокси и пользовательские конфигурации](@/advanced/chains.ru.md#chains) |
| Front / landing proxy (фронт-прокси / выходной прокси) | Параметры группы, которые добавляют по одному дополнительному профилю перед каждым профилем группы (фронт-прокси) или после него (выходной прокси). | [Цепочки прокси и пользовательские конфигурации](@/advanced/chains.ru.md#front-landing) |
| Auto selector (автовыбор) | Тип профиля, который проверяет серверы группы и автоматически использует лучшие из них. | [Тестирование и автовыбор](@/guides/testing.ru.md#auto-selector) |
| URL test (тест задержки, URL-тест) | Тест задержки, который измеряет, сколько времени профилю нужно, чтобы открыть тестовый URL. | [Тестирование и автовыбор](@/guides/testing.ru.md#url-test) |

## Интерфейс {#interface}

| Термин | Значение | Подробнее |
| --- | --- | --- |
| Simple mode (простой режим) | Режим интерфейса новых установок, который скрывает дополнительные пункты меню и параметры. | [Простой и расширенный режим](@/get_started/configuration.ru.md#interface-mode) |
| Advanced mode (расширенный режим) | Режим интерфейса, в котором видны все пункты меню и параметры. | [Простой и расширенный режим](@/get_started/configuration.ru.md#interface-mode) |
| Command palette (палитра команд) | Окно поиска, которое находит команды меню и параметры по названию. | [Палитра команд](@/reference/shortcuts.ru.md#command-palette) |

## Ядро и режимы подключения {#core-and-modes}

| Термин | Значение | Подробнее |
| --- | --- | --- |
| Core (ядро) | ThroneCore — фоновая программа, которая передаёт ваш трафик. | [Что такое Throne](@/get_started/_index.ru.md#what-is-throne) |
| sing-box | Движок с открытым исходным кодом, на котором построен ThroneCore. | [sing-box или Xray](@/advanced/xray.ru.md) |
| Xray | Второй движок, который работает внутри ядра для профилей `VLESS (Xray)` и конфигураций Xray. | [sing-box или Xray](@/advanced/xray.ru.md#vless-preference) |
| Inbound (входящее подключение) | Способ, которым трафик попадает в Throne, например смешанный порт или адаптер TUN. | [Настройки входящих подключений](@/guides/proxy_modes.ru.md#inbound-settings) |
| Outbound (исходящее подключение) | Способ, которым трафик покидает Throne: через профиль (`proxy`), напрямую (`direct`) или никуда (`block`). | [Маршрутизация](@/guides/routing.ru.md#how-routing-works) |
| Mixed port (смешанный порт) | Локальный порт, который принимает подключения как SOCKS5-, так и HTTP-прокси. | [Настройки входящих подключений](@/guides/proxy_modes.ru.md#inbound-settings) |
| System proxy (системный прокси) | Режим `Системный прокси` (System Proxy), который указывает смешанный порт в системных настройках прокси. | [Системный прокси](@/guides/proxy_modes.ru.md#system-proxy) |
| TUN (режим TUN) | `Режим TUN` (Tun Mode), который пропускает трафик всех приложений через виртуальный сетевой адаптер. | [Режим TUN](@/guides/tun_mode.ru.md) |
| Strict route (строгий маршрут) | Параметр TUN, который не даёт трафику идти в обход туннеля. | [Защита от утечек DNS в Windows](@/advanced/windows_tun_mode.ru.md#strict-route) |
| MTU | Максимальный размер пакета для адаптера TUN. | [Параметры TUN](@/guides/tun_mode.ru.md#settings) |

## Маршрутизация и DNS {#routing-and-dns}

| Термин | Значение | Подробнее |
| --- | --- | --- |
| Routing profile (профиль маршрутизации) | Именованный набор правил плюс outbound по умолчанию; одновременно активен только один. | [Маршрутизация](@/guides/routing.ru.md#profiles) |
| Rule (правило) | Условие, например домен или программа, и outbound для подходящих подключений. | [Маршрутизация](@/guides/routing.ru.md#advanced-rules) |
| Rule-set (набор правил) | Готовый список доменов (`geosite-…`) или диапазонов IP-адресов (`geoip-…`) для правил. | [Наборы правил](@/guides/routing.ru.md#rule-sets) |
| Default outbound (outbound по умолчанию) | Куда идёт подключение, если ни одно правило не подошло. | [Маршрутизация](@/guides/routing.ru.md#how-routing-works) |
| warp-bypass | Outbound, который использует ваш профиль, но обходит WARP. | [Cloudflare WARP](@/advanced/warp.ru.md#warp-bypass) |
| Remote routing profile (удалённый профиль маршрутизации) | Профиль маршрутизации, который Throne загружает по URL. | [Удалённые профили](@/guides/routing.ru.md#remote-profiles) |
| Sniffing (сниффинг) | Чтение доменного имени из начала подключения, чтобы правила для доменов работали и для подключений по IP-адресу. | [Маршрутизация](@/guides/routing.ru.md#advanced-rules) |
| DNS routing (DNS-маршрутизация) | Параметр `Включить DNS-маршрутизацию` (Enable DNS Routing), который разрешает домены, подпадающие под правила `direct`, через DNS для прямых запросов. | [DNS](@/guides/dns.ru.md#how-dns-works) |
| Remote DNS / direct DNS (удалённый DNS / DNS для прямых запросов) | DNS-серверы, которые используются через прокси (удалённый) и без него (для прямых запросов). | [Настройки DNS](@/guides/dns.ru.md#settings) |
| FakeIP (поддельные IP) | Режим DNS, который отвечает временными поддельными адресами и сопоставляет их обратно с настоящими доменными именами. | [FakeIP](@/guides/dns.ru.md#fakeip) |

## Протоколы и обход цензуры {#protocols}

| Термин | Значение | Подробнее |
| --- | --- | --- |
| Reality | Параметр безопасности для VLESS, благодаря которому подключение выглядит как посещение настоящего постороннего сайта. | [sing-box или Xray](@/advanced/xray.ru.md#reality) |
| XHTTP | Транспорт Xray, который передаёт подключение внутри обычных HTTP-запросов. | [sing-box или Xray](@/advanced/xray.ru.md#vless-preference) |
| uTLS | Делает рукопожатие TLS похожим на рукопожатие распространённого браузера. | [Пресеты для обхода цензуры](@/advanced/presets.ru.md#utls) |
| ECH | Encrypted Client Hello — скрывает имя сервера внутри рукопожатия TLS. | [Пресеты для обхода цензуры](@/advanced/presets.ru.md#ech) |
| TLS fragment (фрагментация TLS) | Разбивает первое сообщение TLS на мелкие части, чтобы пройти через некоторые фильтры цензуры. | [Пресеты для обхода цензуры](@/advanced/presets.ru.md#tls-fragment) |
| Multiplex (mux) (мультиплексирование) | Передаёт много подключений внутри одного подключения к серверу. | [Пресеты для обхода цензуры](@/advanced/presets.ru.md#multiplex) |
| WARP | Бесплатный VPN-сервис Cloudflare, который Throne может добавить последним звеном после вашего профиля. | [Cloudflare WARP](@/advanced/warp.ru.md) |
| MASQUE | Туннельный протокол поверх HTTP/3 или HTTP/2, доступный как тип профиля и как режим WARP. | [Cloudflare WARP](@/advanced/warp.ru.md) |
| DNS tunnel (DNS-туннель) | Передаёт ваш трафик внутри DNS-запросов, для сетей, где проходит только DNS. | [DNS-туннели](@/advanced/dns_tunnels.ru.md) |
| Resolver (резолвер) | DNS-сервер, который отвечает на запросы за вас, а для DNS-туннеля передаёт их серверу туннеля. | [DNS-туннели](@/advanced/dns_tunnels.ru.md#resolvers) |
| mKCP | Транспорт Xray, который передаёт подключение по UDP. | [DNS-туннели](@/advanced/dns_tunnels.ru.md#xdns) |
| XDNS | DNS-туннель в профиле `VLESS (Xray)`, который отправляет пакеты mKCP как DNS-запросы. | [DNS-туннели](@/advanced/dns_tunnels.ru.md#xdns) |

## Конфиденциальность, файлы и безопасность {#files-and-security}

| Термин | Значение | Подробнее |
| --- | --- | --- |
| HWID | Аппаратный идентификатор, который запрашивают некоторые провайдеры подписок. | [Конфиденциальность и сетевые запросы](@/reference/privacy.ru.md#hwid) |
| OTP (одноразовый пароль) | Одноразовые пароли (коды TOTP или HOTP), которые запрашивают некоторые серверы OpenVPN и OpenConnect. | [Менеджер OTP](@/advanced/vpn_profiles.ru.md#otp-manager) |
| .thrbackup | Файл резервной копии Throne на компьютере и на Android. | [Резервное копирование и восстановление](@/guides/backup.ru.md#backup-restore) |
| Portable mode (портативный режим) | Throne хранит данные рядом с программой, а не в папке пользователя. | [Папка данных](@/reference/files.ru.md#data-folder) |
| Kill switch | Параметр, который блокирует весь трафик, идущий не через Throne. | [Kill Switch](@/advanced/kill_switch.ru.md) |

## Автоматизация и headless {#automation}

| Термин | Значение | Подробнее |
| --- | --- | --- |
| Throne API | Зашифрованный интерфейс, через который скрипты и другие устройства с помощью секретного ключа управляют Throne. | [Throne API и thronectl](@/advanced/api.ru.md) |
| thronectl | Клиент командной строки для Throne API. | [Throne API и thronectl](@/advanced/api.ru.md#commands) |
| Admin request (административный запрос) | Чувствительный запрос к Throne API, на который Throne отвечает только со своего компьютера, если вы не разрешили локальную сеть. | [Административные запросы](@/advanced/api.ru.md#admin-requests) |
| Headless Throne | Throne без окна, для серверов, роутеров и контейнеров. | [Headless Throne](@/headless/_index.ru.md) |

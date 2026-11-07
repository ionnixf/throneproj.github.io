+++
title = "Протоколы и форматы импорта"
description = "Типы профилей, ссылки и файлы, которые Throne импортирует, и как поделиться профилем."
weight = 10
toc = true
+++

## Типы профилей {#protocols}

Создайте профиль через `Программа` (Program) → `Новый профиль` (New profile) (`Ctrl+N`), а на Android — через `Добавить профиль` (Add profile) → `Ручные настройки` (Manual settings). Тип существующего профиля изменить нельзя. `Напрямую` (Direct), `Цепочка прокси` (Chain Proxy), `Доп. ядро` (Extra Core) и пользовательские типы (Custom) есть только в расширенном режиме, см. [Простой и расширенный режим](@/get_started/configuration.ru.md#interface-mode).

| Тип | Примечания |
|---|---|
| `Автовыбор` (Auto Selector) | Выбирает лучший рабочий профиль группы и переключается автоматически, см. [Автовыбор](@/guides/testing.ru.md#auto-selector). |
| `Socks` | SOCKS версии 5 или 4. |
| `HTTP` | При желании поверх TLS (HTTPS). |
| `Shadowsocks` | С шифрами 2022 и плагинами `obfs-local` и `v2ray-plugin`. |
| `VMess` | |
| `Trojan` | |
| `VLESS` | Реализация sing-box, с поддержкой REALITY. |
| `VLESS (Xray)` | Реализация Xray: XHTTP, REALITY, шифрование VLESS, Finalmask, транспорт [XDRIVE](@/advanced/xray.ru.md#xdrive) и mKCP с [XDNS](@/advanced/dns_tunnels.ru.md#xdns). |
| `Hysteria` | Hysteria 1 и 2 в одном типе: версию выбирают в поле `Версия протокола` (Protocol Version). Поддерживает смену портов (port hopping) и обфускацию. Если `Up Mbps` или `Down Mbps` не заполнено, Hysteria 1 использует 10 Мбит/с на отправку и 50 Мбит/с на приём, поэтому укажите реальную скорость вашей линии. |
| `TUIC` | |
| `Juicity` | |
| `Naive` | NaïveProxy поверх HTTPS или QUIC. На Android: `Naïve`. |
| `TrustTunnel` | |
| `AnyTLS` | |
| `Mieru` | |
| `Snell` | Snell версии 4 или 6. |
| `ShadowTLS` | |
| `WireGuard` | Для AmneziaWG отметьте `Включить Amnezia` (Enable Amnezia) (на Android: `Включить AmneziaWG` (Enable AmneziaWG)). `Параметры Amnezia` (Amnezia Options) → `Сгенерировать QUIC-сигнатуру...` (Generate QUIC Signature...) делает рукопожатие похожим на подключение HTTP/3 к выбранному вами домену. Может создать учётную запись Cloudflare WARP, см. [WARP](@/advanced/warp.ru.md#generate). |
| `MASQUE` | HTTP/3 или HTTP/2. Может создать идентификационные данные Cloudflare WARP. |
| `OpenVPN` | Импортирует файлы `.ovpn`. Одноразовые пароли (OTP) работают только на компьютере. См. [OpenVPN](@/advanced/vpn_profiles.ru.md#openvpn). |
| `OpenConnect` | Серверы AnyConnect, GlobalProtect, Fortinet, F5, Pulse и Juniper (`nc`). OTP работает только на компьютере. См. [OpenConnect](@/advanced/vpn_profiles.ru.md#openconnect). |
| `Tailscale` | Только на компьютере. Подключается к вашей сети Tailscale и может использовать выходной узел, см. [Tailscale](@/advanced/vpn_profiles.ru.md#tailscale). |
| `SSH` | |
| `DNSTT / VayDNS` | DNS-туннель к серверу dnstt или VayDNS, см. [DNSTT и VayDNS](@/advanced/dns_tunnels.ru.md#dnstt). |
| `MasterDNS` | DNS-туннель к серверу MasterDnsVPN или StormDNS, см. [MasterDNS](@/advanced/dns_tunnels.ru.md#masterdns). |
| `Напрямую` (Direct) | Без прокси. С интерфейсом для привязки (bind interface) трафик уходит через этот сетевой адаптер. |
| `Пользовательский (sing-box outbound)` (Custom (sing-box outbound)), `Пользовательский (конфигурация sing-box)` (Custom (sing-box config)) | Ваш собственный JSON для sing-box: один outbound или полная конфигурация, которая запускается без изменений. На Android: `Пользовательская конфигурация` (Custom config). См. [Пользовательские конфигурации](@/advanced/chains.ru.md#custom-config). |
| `Пользовательский (Xray outbound)` (Custom (Xray outbound)), `Пользовательский (конфигурация Xray)` (Custom (Xray config)) | То же самое для JSON Xray. |
| `Доп. ядро` (Extra Core) | Только на компьютере. Запускает выбранную вами внешнюю программу и подключается к её локальному SOCKS-порту, см. [Дополнительное ядро](@/advanced/chains.ru.md#extra-core). |
| `Цепочка прокси` (Chain Proxy) | Пропускает трафик последовательно через несколько профилей. На Android: `Прокси-цепочка` (Proxy chain). См. [Цепочки прокси](@/advanced/chains.ru.md#chains). |

`AnyTLS`, `Hysteria`, `Juicity`, `MASQUE`, `ShadowTLS`, `TrustTunnel` и `TUIC` всегда используют TLS, поэтому в их редакторах нет переключателя, который его выключает.

### Какой движок запускает профиль {#engines}

Все типы работают в sing-box, кроме `VLESS (Xray)`, `Пользовательский (Xray outbound)` и `Пользовательский (конфигурация Xray)`, которые работают в движке Xray, встроенном в ядро (XDNS тоже, так как это профиль `VLESS (Xray)`), и `Доп. ядро`, которое запускает выбранную вами программу. Throne для Android использует то же ядро.

`Настройки` (Settings) → `Основные настройки` (Basic Settings) → `Ядро` (Core) → `Предпочтение Xray для VLESS` (Xray VLESS Preference) (только в расширенном режиме) определяет, станет ли импортированная ссылка `vless://` профилем `VLESS` или `VLESS (Xray)`. Ссылки, которым нужна функция, доступная только в Xray (например, шифрование VLESS или mKCP — `type=kcp`), всегда становятся `VLESS (Xray)`. См. [sing-box или Xray](@/advanced/xray.ru.md#vless-preference).

## Форматы импорта {#import-formats}

### Где импортировать {#where-to-import}

| Источник | Компьютер | Android |
|---|---|---|
| Буфер обмена | `Ctrl+V` или `Программа` → `Добавить профиль из буфера обмена` (Add profile from clipboard) | `Добавить профиль` → `Импорт из буфера обмена` (Import from clipboard) |
| Файлы | `Программа` → `Добавить профиль из файла` (Add profile from File(s)) (`Ctrl+O`) или перетащите файлы в главное окно | `Добавить профиль` → `Импортировать из файла` (Import from file) |
| QR-коды | `Программа` → `Сканировать QR-код` (Scan QR Code) (`Ctrl+Shift+Q`) считывает QR-коды на ваших экранах. Изображения с QR-кодами тоже можно импортировать как файлы. | `Добавить профиль` → `Отсканировать QR-код` (Scan QR code) (камерой или через `Выбрать изображение` (Select image)) |
| Подписки | Группа типа `Подписка` (Subscription), см. [Подписки и группы](@/guides/subscriptions.ru.md#add-subscription) | То же самое, в разделе `Группы` (Groups) |
| Ссылки | Ссылки `throne://`, см. [Глубокие ссылки](@/advanced/deeplinks.ru.md) | Ссылки `throne://` и ссылки протоколов, которые вы открываете в других приложениях |

Импортированные профили попадают в текущую группу. Если в буфере обмена находится один URL `http://` или `https://`, Throne спрашивает, добавить ли его как группу подписки, однократно импортировать в текущую группу или использовать как HTTP-прокси.

### Поддерживаемые форматы {#formats}

Оба приложения понимают эти форматы во вставленном тексте, файлах, QR-кодах и ответах подписок.

| Формат | Результат |
|---|---|
| Ссылки для обмена, по одной на строку: `vless://`, `vmess://`, `ss://`, `trojan://`, `socks://`, `socks4://`, `socks4a://`, `socks5://`, `http://`, `https://`, `hysteria://`, `hysteria2://`, `hy2://`, `tuic://`, `juicity://`, `anytls://`, `mieru://`, `mierus://`, `snell://`, `tt://` (TrustTunnel), `shadowtls://`, `wg://`, `wireguard://`, `awg://`, `ssh://`, `naive+https://`, `naive+quic://` | По одному профилю на каждую ссылку |
| Ссылки Throne: `throne://add/…` и более старые `json://…` | Один профиль любого типа |
| Ссылки AmneziaVPN: `vpn://…` | Конфигурации, содержащиеся в ссылке |
| Ссылки DNS-туннелей: `dnst://`, `slipnet://`, `masterdns://`, `stormdns://` | По одному профилю `DNSTT / VayDNS` или `MasterDNS` на ссылку, см. [DNS-туннели](@/advanced/dns_tunnels.ru.md#import) |
| JSON sing-box: полная конфигурация или массив outbound | По одному профилю на каждый поддерживаемый outbound, включая `dnstt` и `masterdns` |
| JSON sing-box: один объект outbound | Один профиль `Пользовательский (sing-box outbound)` |
| JSON Xray: конфигурация, массив outbound или один outbound | `VLESS (Xray)` для outbound VLESS, `Пользовательский (Xray outbound)` для остальных |
| JSON Xray: массив полных конфигураций | По одному профилю `Пользовательский (конфигурация Xray)` на каждую конфигурацию |
| JSON SIP008 (Shadowsocks) | Профили `Shadowsocks` |
| YAML Clash или Mihomo со списком `proxies:` | Профили для записей `socks5`, `http`, `ss`, `vmess`, `vless`, `trojan`, `anytls`, `snell`, `hysteria`, `hysteria2`, `tuic`, `masque` и `ssh` |
| Файл `.conf` WireGuard или AmneziaWG | Один профиль `WireGuard`, названный по комментарию `# Name =` или `# remarks:` либо по комментарию над `[Peer]` |
| Файл `.ovpn` OpenVPN | Один профиль `OpenVPN` |
| XML-профиль AnyConnect, командная строка `openconnect` или файл со строкой `protocol=` | Профили `OpenConnect` |
| Любой из перечисленных форматов в кодировке Base64 | Сначала декодируется, затем импортируется |

`tt://` охватывает оба вида ссылок TrustTunnel: `tt://user:password@server:port?…` и глубокую ссылку `tt://?…` (версии 1 и 2), которую создают приложения TrustTunnel. В ссылке должны быть адрес сервера, имя сервера, имя пользователя и пароль, поэтому ссылка, в которой есть только адрес подписки, отклоняется. Если в ссылке перечислено несколько адресов серверов, Throne импортирует первый и предупреждает об остальных.

Записи, которые не являются прокси-серверами, пропускаются, например outbound sing-box `direct`, `block`, `selector` и `urltest` или outbound Xray `freedom` и `blackhole`. Чтобы запустить полную конфигурацию sing-box или Xray, вставьте её в профиль `Пользовательский (конфигурация sing-box)` (Custom (sing-box config)) или `Пользовательский (конфигурация Xray)` (Custom (Xray config)), см. [Полные конфигурации](@/advanced/chains.ru.md#full-configs).

Файлы для импорта больше 50 MB (на Android это ограничение не касается файлов `.zip`) и ответы подписок больше 64 MB отклоняются.

### Значения, которые нельзя использовать {#unusable-values}

Эти значения не останавливают импорт. Throne пропускает такое значение, импортирует остальную часть профиля и пишет предупреждение в [отчёт об изменениях](@/guides/subscriptions.ru.md#change-report):

- `flow`, который профиль `VLESS` или `VLESS (Xray)` не может использовать, например Vision на транспорте, который его не поддерживает;
- неверная настройка XHTTP (неверный режим XHTTP становится `auto`);
- неверный `spx` (spider X) у REALITY;
- неверный порт или диапазон портов Mieru.

### Отличия на Android {#android-import}

- URL подписки также может быть адресом `content://`, который указывает на файл на устройстве.
- `Импортировать из файла` (Import from file) принимает и файл `.zip` и импортирует все файлы внутри него, например несколько конфигураций WireGuard или OpenVPN.
- Ссылки `clash://install-config?url=…` открывают Throne и добавляют URL как подписку.
- Если в другом приложении нажать на ссылку протокола (`vless://…`) или на ссылку `throne://`, Throne откроется и перед импортом профиля спросит подтверждение.
- Системное меню «Поделиться» в Android предлагает Throne только для двоичных файлов, например резервных копий. Ссылки из мессенджеров копируйте и импортируйте через `Импорт из буфера обмена` (Import from clipboard), другие файлы открывайте через `Импортировать из файла`.
- Профили `Tailscale` и `Доп. ядро` (Extra Core) на Android использовать нельзя.

## Ссылки для обмена {#share-links}

### Типы ссылок {#link-types}

| Ссылка | Как выглядит | Кто может её прочитать |
|---|---|---|
| Стандартная ссылка | `vless://…`, `ss://…`, `hysteria2://…` и так далее | Throne и большинство других клиентов |
| Ссылка Throne | `throne://add/…` | Throne для компьютера и Throne для Android |
| Старая ссылка Throne | `json://…` | Throne, только для импорта |

**Стандартная ссылка** использует обычный формат ссылок протокола. У `Автовыбор` (Auto Selector), `Цепочка прокси` (Chain Proxy), типов `Пользовательский (…)` (Custom), `Напрямую` (Direct), `Доп. ядро` (Extra Core), `MASQUE`, `OpenVPN`, `OpenConnect` и `VLESS (Xray)` с XDRIVE её нет, поэтому Throne обычно выдаёт вместо неё ссылку Throne. `Tailscale` показывает ссылку `ts://`, которую Throne не может импортировать, поэтому передавайте его ссылку Throne. Профили `DNSTT / VayDNS` и `MasterDNS` дают ссылку `dnst://`, см. [DNS-туннели](@/advanced/dns_tunnels.ru.md#import).

**Ссылка Throne** содержит настройки профиля в виде JSON, закодированного в Base64. Она работает для любого типа, но прочитать её может только Throne, см. [Глубокие ссылки](@/advanced/deeplinks.ru.md#add). `json://` — её старый формат, который Throne по-прежнему импортирует.

Ссылка на профиль `Цепочка прокси` или `Автовыбор` хранит только внутренние номера профилей или группы, которые он использует, поэтому работает только в той же установке Throne.

### ECH и закреплённые сертификаты в ссылках {#ech-links}

Throne читает и записывает в стандартных ссылках такие параметры TLS:

| Параметр | Значение |
|---|---|
| `ech=1` или `ech=true` | Включает ECH. Ядро ищет конфигурацию ECH в DNS. |
| `ech=` домен или домен, `+` и URL DNS-сервера | Включает ECH и указывает домен для поиска и при необходимости DNS-сервер для запроса, например `cloudflare-ech.com+https://1.1.1.1/dns-query`. Заполняет поле `ECH Query Name / Resolver`. |
| `ech=` любое другое значение | Включает ECH с этой конфигурацией ECH в Base64. |
| `pcs=` | Хеши SHA-256 сертификата сервера в шестнадцатеричном виде через запятую. Совпадающий сертификат принимается вместо обычной проверки по удостоверяющему центру. Заполняет поле `Certificate SHA256`. |

Для профилей `VLESS (Xray)` значение `ech` без изменений попадает в `ECH Config List`. Ссылки, которые создаёт Throne, также сохраняют старые параметры `ech_enabled`, `ech_config` и `ech_server_name`. О том, что делает ECH, см. [ECH](@/advanced/presets.ru.md#ech).

### Обмен на компьютере {#share-desktop}

Щёлкните профиль правой кнопкой мыши и откройте `Поделиться` (Share):

| Действие | Что вы получаете |
|---|---|
| `QR-код и ссылка` (QR Code and link) | Стандартную ссылку и её QR-код. Отметьте `Deep Link`, чтобы получить ссылку Throne. |
| `Копировать ссылки выбранных` (Copy links of selected) (`Ctrl+C`) | Стандартные ссылки выбранных профилей, по одной на строку. |
| `Копировать ссылки выбранных (deep links)` (Copy links of selected (Deep Links)) (`Ctrl+Alt+C`) | Ссылки Throne выбранных профилей. |
| `Экспортировать конфиг` (Export config) (`Ctrl+E`) | Конфигурацию ядра, которую Throne создаёт для профиля, на вкладке `sing-box` и, для профилей, использующих Xray, на вкладке `Xray`. Это конфигурация для других клиентов, а не ссылка для обмена. |

Ссылки всей группы копируются через `Группы` (Groups) → `Редактировать текущую группу` (Edit current Group) → `Копировать ссылки профилей` (Copy profile share links) или `Копировать ссылки профилей (deep links)` (Copy profile share links (Deep Links)). `Копировать ссылки выбранных (deep links)` и `Экспортировать конфиг` есть только в расширенном режиме, см. [Простой и расширенный режим](@/get_started/configuration.ru.md#interface-mode).

### Обмен на Android {#share-android}

Нажмите кнопку «Поделиться» у профиля, а у карточек в два столбца и в режиме `Компактные строки профилей` (Compact profile rows) — ⋮ → `Поделиться` (Share):

- `QR-код` (QR code) → `Стандарт` (Standard) или `Ссылка Throne` (Throne link)
- `Экспорт в буфер обмена` (Export to clipboard) → `Стандарт` или `Ссылка Throne`
- `Конфигурация` (Configuration) → `Экспорт в буфер обмена` или `Экспорт в файл` (Export to file): конфигурация ядра, которую создаёт Throne, а не ссылка для обмена

Пункта `Стандарт` нет, если у типа профиля нет стандартной ссылки, а у цепочки есть только `Конфигурация`. Чтобы скопировать ссылки всей группы, откройте `Группы` (Groups), нажмите `Другие параметры` (More options) у группы и выберите `Копировать ссылки для обмена профилями` (Copy profile share links) или `Копировать ссылки для обмена профилями (deep link)` (Copy profile share links (deep links)).

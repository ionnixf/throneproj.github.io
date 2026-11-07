+++
title = "پروتکل‌ها و قالب‌های واردکردن"
description = "انواع نمایه، لینک‌ها و فایل‌هایی که Throne وارد می‌کند و روش اشتراک‌گذاری یک نمایه."
weight = 10
toc = true
+++

## انواع نمایه {#protocols}

یک نمایه را با `برنامه` (Program) ← `نمایه جدید` (New profile) (`Ctrl+N`)، یا در Android با `اضافه‌کردن پراکسی جدید` (Add profile) ← `تنظیمات دستی` (Manual settings) بسازید. نوع یک نمایهٔ موجود را نمی‌توان تغییر داد. `مستقیم` (Direct)، `پروکسی زنجیره ای` (Chain Proxy)، `هسته اضافی` (Extra Core) و انواع `سفارشی` (Custom) فقط در حالت پیشرفته هستند؛ [حالت ساده و پیشرفته](@/get_started/configuration.fa.md#interface-mode) را ببینید.

| نوع | توضیحات |
|---|---|
| `Auto Selector` | بهترین نمایهٔ سالمِ یک گروه را انتخاب می‌کند و به‌طور خودکار میان نمایه‌ها جابه‌جا می‌شود؛ [انتخابگر خودکار](@/guides/testing.fa.md#auto-selector) را ببینید. |
| `Socks` | SOCKS نسخهٔ 5 یا 4. |
| `HTTP` | به‌صورت اختیاری روی TLS (HTTPS). |
| `Shadowsocks` | با روش‌های رمزنگاری 2022 و افزونه‌های `obfs-local` و `v2ray-plugin`. |
| `VMess` | |
| `Trojan` | |
| `VLESS` | پیاده‌سازی sing-box، با REALITY. |
| `VLESS (Xray)` | پیاده‌سازی Xray: XHTTP، REALITY، رمزنگاری VLESS، Finalmask، ترنسپورت [XDRIVE](@/advanced/xray.fa.md#xdrive) و mKCP همراه با [XDNS](@/advanced/dns_tunnels.fa.md#xdns). |
| `Hysteria` | Hysteria 1 و 2 در یک نوع: `Protocol Version` را انتخاب کنید. از پرش پورت (port hopping) و مبهم‌سازی (obfuscation) پشتیبانی می‌کند. وقتی `Up Mbps` یا `Down Mbps` خالی باشد، Hysteria 1 از 10 Mbps برای ارسال و 50 Mbps برای دریافت استفاده می‌کند؛ پس سرعت واقعی خط خود را وارد کنید. |
| `TUIC` | |
| `Juicity` | |
| `Naive` | NaïveProxy روی HTTPS یا QUIC. در Android: `Naïve`. |
| `TrustTunnel` | |
| `AnyTLS` | |
| `Mieru` | |
| `Snell` | Snell نسخهٔ 4 یا 6. |
| `ShadowTLS` | |
| `WireGuard` | برای AmneziaWG، تیک `Enable Amnezia` را بزنید (در Android: `فعال‌سازی AmneziaWG` (Enable AmneziaWG)). `Amnezia Options` → `Generate QUIC Signature...` باعث می‌شود handshake مانند یک اتصال HTTP/3 به دامنه‌ای که انتخاب می‌کنید به نظر برسد. می‌تواند یک حساب Cloudflare WARP بسازد؛ [WARP](@/advanced/warp.fa.md#generate) را ببینید. |
| `MASQUE` | HTTP/3 یا HTTP/2. می‌تواند یک هویت Cloudflare WARP بسازد. |
| `OpenVPN` | فایل‌های `.ovpn` را وارد می‌کند. گذرواژه‌های یک‌بارمصرف (OTP) فقط در دسکتاپ کار می‌کنند. [OpenVPN](@/advanced/vpn_profiles.fa.md#openvpn) را ببینید. |
| `OpenConnect` | سرورهای AnyConnect، GlobalProtect، Fortinet، F5، Pulse و Juniper (`nc`). OTP فقط در دسکتاپ کار می‌کند. [OpenConnect](@/advanced/vpn_profiles.fa.md#openconnect) را ببینید. |
| `Tailscale` | فقط دسکتاپ. به شبکهٔ Tailscale شما می‌پیوندد و می‌تواند از یک گرهٔ خروجی (exit node) استفاده کند؛ [Tailscale](@/advanced/vpn_profiles.fa.md#tailscale) را ببینید. |
| `SSH` | |
| `DNSTT / VayDNS` | تونل DNS به یک سرور dnstt یا VayDNS؛ [DNSTT و VayDNS](@/advanced/dns_tunnels.fa.md#dnstt) را ببینید. |
| `MasterDNS` | تونل DNS به یک سرور MasterDnsVPN یا StormDNS؛ [MasterDNS](@/advanced/dns_tunnels.fa.md#masterdns) را ببینید. |
| `مستقیم` (Direct) | بدون پروکسی. با یک رابط شبکه (bind interface)، ترافیک را از طریق همان آداپتور شبکه خارج می‌کند. |
| `سفارشی (sing-box خروجی)` (Custom (sing-box outbound))، `سفارشی (sing-box پیکربندی)` (Custom (sing-box config)) | JSON دلخواه خودتان برای sing-box: یک خروجی (outbound)، یا یک پیکربندی کامل که بدون تغییر اجرا می‌شود. در Android: `پیکربندی اختصاصی` (Custom config). [پیکربندی‌های سفارشی](@/advanced/chains.fa.md#custom-config) را ببینید. |
| `Custom (Xray outbound)`، `Custom (Xray config)` | همین موارد برای JSON مربوط به Xray. |
| `هسته اضافی` (Extra Core) | فقط دسکتاپ. یک برنامهٔ خارجی را که انتخاب می‌کنید اجرا می‌کند و به پورت SOCKS محلی آن وصل می‌شود؛ [هستهٔ اضافی](@/advanced/chains.fa.md#extra-core) را ببینید. |
| `پروکسی زنجیره ای` (Chain Proxy) | ترافیک را به‌ترتیب از چند نمایه عبور می‌دهد. در Android: `زنجیره پراکسی` (Proxy chain). [زنجیره‌ها](@/advanced/chains.fa.md#chains) را ببینید. |

`AnyTLS`، `Hysteria`، `Juicity`، `MASQUE`، `ShadowTLS`، `TrustTunnel` و `TUIC` همیشه از TLS استفاده می‌کنند؛ بنابراین ویرایشگر آن‌ها کلیدی برای خاموش کردن آن ندارد.

### کدام موتور یک نمایه را اجرا می‌کند {#engines}

همهٔ انواع در sing-box اجرا می‌شوند، به‌جز `VLESS (Xray)`، `Custom (Xray outbound)` و `Custom (Xray config)` که در موتور Xray درون هسته اجرا می‌شوند (XDNS هم، چون یک نمایهٔ `VLESS (Xray)` است)، و `هسته اضافی` (Extra Core) که برنامه‌ای را که انتخاب می‌کنید اجرا می‌کند. Throne برای Android از همین هسته استفاده می‌کند.

گزینهٔ `تنظیمات` (Settings) ← `تنظیمات پایه` (Basic Settings) ← `هسته` (Core) ← `Xray VLESS Preference` (فقط در حالت پیشرفته) تعیین می‌کند که یک لینک `vless://` واردشده به `VLESS` تبدیل شود یا به `VLESS (Xray)`. لینک‌هایی که به قابلیتی نیاز دارند که فقط Xray دارد، مانند رمزنگاری VLESS یا mKCP (`type=kcp`)، همیشه به `VLESS (Xray)` تبدیل می‌شوند. [sing-box در برابر Xray](@/advanced/xray.fa.md#vless-preference) را ببینید.

## قالب‌های واردکردن {#import-formats}

### کجا وارد کنید {#where-to-import}

| منبع | دسکتاپ | Android |
|---|---|---|
| کلیپ‌بورد | `Ctrl+V`، یا `برنامه` ← `افزودن نمایه از کلیپ بورد` (Add profile from clipboard) | `اضافه‌کردن پراکسی جدید` ← `وارد کردن از بریده‌دان` (Import from clipboard) |
| فایل‌ها | `برنامه` ← `Add profile from File(s)` (`Ctrl+O`)، یا کشیدن و رها کردن فایل‌ها روی پنجرهٔ اصلی | `اضافه‌کردن پراکسی جدید` ← `وارد کردن از یک فایل` (Import from file) |
| کدهای QR | `برنامه` ← `کد QR را اسکن کنید` (Scan QR Code) (`Ctrl+Shift+Q`) کدهای QR روی صفحه‌نمایش‌های شما را می‌خواند. تصاویر QR به‌عنوان فایل هم کار می‌کنند. | `اضافه‌کردن پراکسی جدید` ← `اسکن از کد QR` (Scan QR code) (با دوربین، یا `انتخاب تصویر` (Select image)) |
| اشتراک‌ها | گروهی از نوع `اشتراک` (Subscription)؛ [اشتراک‌ها و گروه‌ها](@/guides/subscriptions.fa.md#add-subscription) را ببینید | همین روش، در `گروه‌ها` (Groups) |
| لینک‌ها | لینک‌های `throne://`؛ [لینک‌های عمیق](@/advanced/deeplinks.fa.md) را ببینید | لینک‌های `throne://` و لینک‌های پروتکلی که در برنامه‌های دیگر باز می‌کنید |

نمایه‌های واردشده به گروه جاری اضافه می‌شوند. اگر کلیپ‌بورد فقط یک نشانی `http://` یا `https://` داشته باشد، Throne می‌پرسد که آن را به‌عنوان گروه اشتراک اضافه کند، یک‌بار در گروه جاری وارد کند یا به‌عنوان پروکسی HTTP به کار ببرد.

### قالب‌های پشتیبانی‌شده {#formats}

هر دو برنامه این قالب‌ها را در متن جای‌گذاری‌شده، فایل‌ها، کدهای QR و پاسخ‌های اشتراک می‌خوانند.

| قالب | نتیجه |
|---|---|
| لینک‌های اشتراک‌گذاری، هر لینک در یک خط: `vless://`، `vmess://`، `ss://`، `trojan://`، `socks://`، `socks4://`، `socks4a://`، `socks5://`، `http://`، `https://`، `hysteria://`، `hysteria2://`، `hy2://`، `tuic://`، `juicity://`، `anytls://`، `mieru://`، `mierus://`، `snell://`، `tt://` (TrustTunnel)، `shadowtls://`، `wg://`، `wireguard://`، `awg://`، `ssh://`، `naive+https://`، `naive+quic://` | یک نمایه برای هر لینک |
| لینک‌های Throne: `throne://add/…` و قالب قدیمی‌تر `json://…` | یک نمایه از هر نوعی |
| لینک‌های AmneziaVPN: `vpn://…` | پیکربندی‌های درون لینک |
| لینک‌های تونل DNS: `dnst://`، `slipnet://`، `masterdns://`، `stormdns://` | یک نمایهٔ `DNSTT / VayDNS` یا `MasterDNS` برای هر لینک؛ [تونل‌های DNS](@/advanced/dns_tunnels.fa.md#import) را ببینید |
| JSON مربوط به sing-box: یک پیکربندی کامل، یا آرایه‌ای از خروجی‌ها (outbound) | یک نمایه برای هر خروجی پشتیبانی‌شده، از جمله `dnstt` و `masterdns` |
| JSON مربوط به sing-box: یک شیء خروجی | یک نمایهٔ `سفارشی (sing-box خروجی)` |
| JSON مربوط به Xray: یک پیکربندی، آرایه‌ای از خروجی‌ها، یا یک خروجی | `VLESS (Xray)` برای خروجی‌های VLESS و `Custom (Xray outbound)` برای بقیه |
| JSON مربوط به Xray: آرایه‌ای از پیکربندی‌های کامل | یک نمایهٔ `Custom (Xray config)` برای هر پیکربندی |
| JSON قالب SIP008 (Shadowsocks) | نمایه‌های `Shadowsocks` |
| YAML مربوط به Clash یا Mihomo با فهرست `proxies:` | نمایه‌هایی برای موارد `socks5`، `http`، `ss`، `vmess`، `vless`، `trojan`، `anytls`، `snell`، `hysteria`، `hysteria2`، `tuic`، `masque` و `ssh` |
| فایل `.conf` مربوط به WireGuard یا AmneziaWG | یک نمایهٔ `WireGuard` که نامش از یک توضیح `# Name =` یا `# remarks:`، یا از توضیح بالای `[Peer]` گرفته می‌شود |
| فایل `.ovpn` مربوط به OpenVPN | یک نمایهٔ `OpenVPN` |
| نمایهٔ XML مربوط به AnyConnect، یک خط فرمان `openconnect`، یا فایلی با خط `protocol=` | نمایه‌های `OpenConnect` |
| هر یک از موارد بالا، کدگذاری‌شده با Base64 | ابتدا رمزگشایی و سپس وارد می‌شود |

`tt://` هر دو نوع لینک TrustTunnel را در بر می‌گیرد: `tt://user:password@server:port?…` و لینک عمیق `tt://?…` (نسخه‌های 1 و 2) که برنامه‌های TrustTunnel می‌سازند. لینک باید نشانی سرور، نام سرور، نام کاربری و گذرواژه را داشته باشد؛ بنابراین لینکی که فقط نشانی یک اشتراک را دارد پذیرفته نمی‌شود. اگر لینک چند نشانی سرور داشته باشد، Throne اولی را وارد می‌کند و دربارهٔ بقیه هشدار می‌دهد.

مواردی که سرور پروکسی نیستند نادیده گرفته می‌شوند، مانند خروجی‌های `direct`، `block`، `selector` و `urltest` در sing-box یا `freedom` و `blackhole` در Xray. برای اجرای یک پیکربندی کامل sing-box یا Xray، آن را در یک نمایهٔ `سفارشی (sing-box پیکربندی)` یا `Custom (Xray config)` جای‌گذاری کنید؛ [پیکربندی‌های کامل](@/advanced/chains.fa.md#full-configs) را ببینید.

فایل‌های واردکردنِ بزرگ‌تر از 50 MB (در Android، فایل‌های `.zip` محدودیتی ندارند) و پاسخ‌های اشتراکِ بزرگ‌تر از 64 MB پذیرفته نمی‌شوند.

### مقدارهایی که قابل استفاده نیستند {#unusable-values}

این مقدارها جلوی وارد کردن را نمی‌گیرند. Throne آن مقدار را کنار می‌گذارد، بقیهٔ نمایه را وارد می‌کند و در [گزارش تغییرات](@/guides/subscriptions.fa.md#change-report) هشدار می‌دهد:

- `flow` که نمایهٔ `VLESS` یا `VLESS (Xray)` نمی‌تواند به کار ببرد، برای مثال Vision روی ترنسپورتی که از آن پشتیبانی نمی‌کند.
- یک تنظیم نامعتبر XHTTP (حالت نامعتبر XHTTP به `auto` تبدیل می‌شود).
- یک `spx` (spider X) نامعتبر در REALITY.
- یک پورت یا بازهٔ پورت نامعتبر در Mieru.

### تفاوت‌ها در Android {#android-import}

- نشانی اشتراک می‌تواند یک نشانی `content://` هم باشد که به فایلی روی دستگاه اشاره می‌کند.
- `وارد کردن از یک فایل` فایل `.zip` را هم می‌پذیرد و همهٔ فایل‌های درون آن را وارد می‌کند؛ برای مثال چند پیکربندی WireGuard یا OpenVPN.
- لینک‌های `clash://install-config?url=…` Throne را باز می‌کنند و آن نشانی را به‌عنوان اشتراک اضافه می‌کنند.
- وقتی در برنامهٔ دیگری روی یک لینک پروتکل (`vless://…`) یا یک لینک `throne://` می‌زنید، Throne باز می‌شود و پیش از واردکردن نمایه از شما می‌پرسد.
- برگهٔ اشتراک‌گذاری Android (share sheet) فقط برای فایل‌های باینری، مانند فایل‌های پشتیبان، Throne را پیشنهاد می‌دهد. لینک‌ها را از برنامه‌های پیام‌رسان کپی کنید و از `وارد کردن از بریده‌دان` استفاده کنید؛ فایل‌های دیگر را با `وارد کردن از یک فایل` باز کنید.
- نمایه‌های `Tailscale` و `هسته اضافی` در Android قابل استفاده نیستند.

## لینک‌های اشتراک‌گذاری {#share-links}

### انواع لینک {#link-types}

| لینک | شکل ظاهری | چه برنامه‌هایی می‌توانند آن را بخوانند |
|---|---|---|
| لینک استاندارد | `vless://…`، `ss://…`، `hysteria2://…` و مانند این‌ها | Throne و بیشتر کلاینت‌های دیگر |
| لینک Throne | `throne://add/…` | Throne دسکتاپ و Throne برای Android |
| لینک قدیمی Throne | `json://…` | Throne، فقط برای واردکردن |

**لینک استاندارد** از قالب معمول لینک در آن پروتکل استفاده می‌کند. `Auto Selector`، `پروکسی زنجیره ای`، انواع `سفارشی` (Custom)، `مستقیم`، `هسته اضافی`، `MASQUE`، `OpenVPN`، `OpenConnect` و `VLESS (Xray)` با ترنسپورت XDRIVE لینک استاندارد ندارند؛ بنابراین Throne معمولاً لینک Throne را به‌جای آن می‌دهد. `Tailscale` یک لینک `ts://` نشان می‌دهد که Throne نمی‌تواند آن را وارد کند؛ پس لینک Throne آن را به اشتراک بگذارید. نمایه‌های `DNSTT / VayDNS` و `MasterDNS` یک لینک `dnst://` می‌دهند؛ [تونل‌های DNS](@/advanced/dns_tunnels.fa.md#import) را ببینید.

**لینک Throne** تنظیمات نمایه را به‌صورت JSON کدگذاری‌شده با Base64 در خود دارد. برای همهٔ انواع کار می‌کند، اما فقط Throne می‌تواند آن را بخواند؛ [لینک‌های عمیق](@/advanced/deeplinks.fa.md#add) را ببینید. `json://` قالب قدیمی‌تر آن است که Throne هنوز آن را وارد می‌کند.

لینک یک نمایهٔ `پروکسی زنجیره ای` یا `Auto Selector` فقط شماره‌های داخلی نمایه‌ها یا گروهی را که استفاده می‌کند ذخیره می‌کند؛ بنابراین فقط در همان نصب Throne کار می‌کند.

### ECH و پین گواهی در لینک‌ها {#ech-links}

Throne این پارامترهای TLS را در لینک‌های استاندارد می‌خواند و می‌نویسد:

| پارامتر | معنا |
|---|---|
| `ech=1` یا `ech=true` | ECH را روشن می‌کند. هسته پیکربندی ECH را در DNS جست‌وجو می‌کند. |
| `ech=` یک دامنه، یا یک دامنه، `+` و نشانی یک سرور DNS | ECH را روشن می‌کند و دامنه‌ای را که باید جست‌وجو شود، و در صورت نیاز سرور DNS‌ای را که باید پرسیده شود، مشخص می‌کند؛ برای مثال `cloudflare-ech.com+https://1.1.1.1/dns-query`. این مقدار در `ECH Query Name / Resolver` قرار می‌گیرد. |
| `ech=` هر مقدار دیگر | ECH را با این پیکربندی ECH کدگذاری‌شده با Base64 روشن می‌کند. |
| `pcs=` | هش‌های SHA-256 گواهی سرور، به‌صورت hex و جداشده با ویرگول. گواهی منطبق به‌جای بررسی معمول CA پذیرفته می‌شود. این مقدار در `Certificate SHA256` قرار می‌گیرد. |

برای نمایه‌های `VLESS (Xray)`، مقدار `ech` بدون تغییر در `ECH Config List` قرار می‌گیرد. لینک‌هایی که Throne می‌سازد پارامترهای قدیمی‌تر `ech_enabled`، `ech_config` و `ech_server_name` را هم نگه می‌دارند. برای اینکه ECH چه می‌کند، [ECH](@/advanced/presets.fa.md#ech) را ببینید.

### اشتراک‌گذاری در دسکتاپ {#share-desktop}

روی یک نمایه راست‌کلیک کنید و `اشتراک گذاری` (Share) را باز کنید:

| عمل | نتیجه |
|---|---|
| `کد QR و پیوند` (QR Code and link) | لینک استاندارد و کد QR آن. برای لینک Throne، تیک `Deep Link` را بزنید. |
| `لینک های انتخاب شده را کپی کنید` (Copy links of selected) (`Ctrl+C`) | لینک‌های استاندارد نمایه‌های انتخاب‌شده، هر کدام در یک خط. |
| `Copy links of selected (Deep Links)` (`Ctrl+Alt+C`) | لینک‌های Throne نمایه‌های انتخاب‌شده. |
| `استخراج پیکربندی` (Export config) (`Ctrl+E`) | پیکربندی هسته‌ای که Throne برای نمایه تولید می‌کند، در یک زبانهٔ `sing-box` و، برای نمایه‌هایی که از Xray استفاده می‌کنند، یک زبانهٔ `Xray`. پیکربندی‌ای برای کلاینت‌های دیگر است، نه لینک اشتراک‌گذاری. |

برای لینک‌های یک گروه کامل، از `گروه ها` (Groups) ← `Edit current Group` ← `لینک های اشتراک گذاری نمایه را کپی کنید` (Copy profile share links) یا `Copy profile share links (Deep Links)` استفاده کنید. `Copy links of selected (Deep Links)` و `استخراج پیکربندی` (Export config) فقط در حالت پیشرفته هستند؛ [حالت ساده و پیشرفته](@/get_started/configuration.fa.md#interface-mode) را ببینید.

### اشتراک‌گذاری در Android {#share-android}

روی دکمهٔ اشتراک‌گذاری یک نمایه بزنید، یا در کارت‌های دوستونه و `ردیف‌های فشردهٔ پروفایل` (Compact profile rows) روی ⋮ ← `اشتراک‌گذاری` (Share) بزنید:

- `کد QR` (QR code) ← `استاندارد` (Standard) یا `پیوند Throne` (Throne link)
- `صدور در بریده‌دان` (Export to clipboard) ← `استاندارد` یا `پیوند Throne`
- `پیکربندی` (Configuration) ← `صدور در بریده‌دان` یا `صدور به صورت فایل` (Export to file): پیکربندی هسته‌ای که Throne تولید می‌کند، نه لینک اشتراک‌گذاری

اگر نوع نمایه لینک استاندارد نداشته باشد، `استاندارد` نمایش داده نمی‌شود، و زنجیره فقط `پیکربندی` را دارد. برای لینک‌های یک گروه کامل، `گروه‌ها` را باز کنید، روی `گزینه‌های بیشتر` (More options) در آن گروه بزنید و `کپی پیوندهای اشتراک‌گذاری پروفایل` (Copy profile share links) یا `کپی پیوندهای اشتراک‌گذاری پروفایل (پیوندهای عمیق)` (Copy profile share links (deep links)) را انتخاب کنید.

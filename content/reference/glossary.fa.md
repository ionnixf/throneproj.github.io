+++
title = "واژه‌نامه"
description = "توضیح کوتاه اصطلاحاتی که در Throne با آن‌ها روبه‌رو می‌شوید."
weight = 50
toc = true
+++

## نمایه‌ها و گروه‌ها {#profiles-and-groups}

| اصطلاح | معنی | بیشتر |
| --- | --- | --- |
| Profile (نمایه) | یک سرور یا پروکسی در فهرست، همراه با پروتکل، نشانی و تنظیمات آن. | [شروع سریع](@/get_started/configuration.fa.md#add-servers) |
| Group (گروه) | زبانه‌ای در پنجرهٔ اصلی که نمایه‌ها را در خود دارد. | [اشتراک‌ها و گروه‌ها](@/guides/subscriptions.fa.md#group-options) |
| Subscription (اشتراک) | یک URL از ارائه‌دهندهٔ شما که فهرستی از نمایه‌ها را برمی‌گرداند و Throne آن‌ها را در یک گروه دانلود می‌کند. | [اشتراک‌ها و گروه‌ها](@/guides/subscriptions.fa.md#add-subscription) |
| Share link (لینک اشتراک‌گذاری) | یک لینک تک‌خطی برای یک نمایه، مانند `vless://…`، که برنامه‌های دیگر هم آن را می‌فهمند. | [پروتکل‌ها و قالب‌های واردکردن](@/reference/protocols.fa.md#share-links) |
| Deep link (لینک عمیق) | یک لینک `throne://` که به Throne می‌گوید کاری انجام دهد، مانند افزودن یک اشتراک. | [لینک‌های عمیق](@/advanced/deeplinks.fa.md#format) |
| Throne link (لینک Throne) | یک لینک عمیق `throne://add/…` که یک نمایه از هر نوعی را در خود دارد. | [لینک‌های عمیق](@/advanced/deeplinks.fa.md#add) |
| Chain (زنجیره) | نمایهٔ `پروکسی زنجیره ای` (Chain Proxy) که ترافیک را به‌ترتیب از چند نمایه عبور می‌دهد. | [زنجیره‌های پروکسی و پیکربندی‌های سفارشی](@/advanced/chains.fa.md#chains) |
| Front / landing proxy (پروکسی front / پروکسی مقصد) | گزینه‌هایی در گروه که یک نمایهٔ اضافه را پیش از (front) یا پس از (landing) هر نمایهٔ گروه قرار می‌دهند. | [زنجیره‌های پروکسی و پیکربندی‌های سفارشی](@/advanced/chains.fa.md#front-landing) |
| Auto selector (انتخابگر خودکار) | نوعی نمایه که سرورهای یک گروه را تست می‌کند و به‌طور خودکار از بهترین‌ها استفاده می‌کند. | [تست و انتخابگر خودکار](@/guides/testing.fa.md#auto-selector) |
| URL test (تست URL) | یک تست تأخیر که اندازه می‌گیرد باز کردن یک URL آزمایشی از طریق یک نمایه چقدر طول می‌کشد. | [تست و انتخابگر خودکار](@/guides/testing.fa.md#url-test) |

## رابط کاربری {#interface}

| اصطلاح | معنی | بیشتر |
| --- | --- | --- |
| Simple mode (حالت ساده) | حالت رابط کاربری در نصب‌های تازه، که گزینه‌های پیشرفتهٔ منوها و تنظیمات را پنهان می‌کند. | [حالت ساده و پیشرفته](@/get_started/configuration.fa.md#interface-mode) |
| Advanced mode (حالت پیشرفته) | حالتی از رابط کاربری که همهٔ گزینه‌های منوها و تنظیمات را نشان می‌دهد. | [حالت ساده و پیشرفته](@/get_started/configuration.fa.md#interface-mode) |
| Command palette (پالت فرمان) | پنجره‌ای برای جست‌وجو که فرمان‌های منوها و تنظیمات را با نامشان پیدا می‌کند. | [پالت فرمان](@/reference/shortcuts.fa.md#command-palette) |

## هسته و حالت‌های اتصال {#core-and-modes}

| اصطلاح | معنی | بیشتر |
| --- | --- | --- |
| Core (هسته) | ThroneCore، برنامهٔ پس‌زمینه‌ای که ترافیک شما را منتقل می‌کند. | [Throne چیست](@/get_started/_index.fa.md#what-is-throne) |
| sing-box | موتور متن‌بازی که ThroneCore بر پایهٔ آن ساخته شده است. | [sing-box در برابر Xray](@/advanced/xray.fa.md) |
| Xray | موتور دومی که درون هسته، برای نمایه‌های `VLESS (Xray)` و پیکربندی‌های Xray اجرا می‌شود. | [sing-box در برابر Xray](@/advanced/xray.fa.md#vless-preference) |
| Inbound (ورودی) | راهی برای ورود ترافیک به Throne، مانند پورت mixed یا آداپتور TUN. | [تنظیمات ورودی](@/guides/proxy_modes.fa.md#inbound-settings) |
| Outbound (خروجی) | راهی برای خروج ترافیک از Throne: از طریق یک نمایه (`proxy`)، مستقیم (`direct`) یا هیچ‌جا (`block`). | [مسیریابی](@/guides/routing.fa.md#how-routing-works) |
| Mixed port (پورت mixed) | پورت محلی که هم اتصال‌های پروکسی SOCKS5 و هم HTTP را می‌پذیرد. | [تنظیمات ورودی](@/guides/proxy_modes.fa.md#inbound-settings) |
| System proxy (پروکسی سیستمی) | حالت `پروکسی سیستمی` (System Proxy) که تنظیم پروکسی سیستم شما را به پورت mixed هدایت می‌کند. | [پروکسی سیستمی](@/guides/proxy_modes.fa.md#system-proxy) |
| TUN (حالت TUN) | گزینهٔ `حالتvpn` (Tun Mode) که ترافیک همهٔ برنامه‌ها را از یک آداپتور شبکهٔ مجازی عبور می‌دهد. | [حالت TUN](@/guides/tun_mode.fa.md) |
| Strict route (مسیر سخت‌گیرانه) | گزینه‌ای در حالت TUN که مانع می‌شود ترافیک تونل را دور بزند. | [محافظت از نشت DNS در Windows](@/advanced/windows_tun_mode.fa.md#strict-route) |
| MTU | بزرگ‌ترین اندازهٔ بسته در آداپتور TUN. | [تنظیمات TUN](@/guides/tun_mode.fa.md#settings) |

## مسیریابی و DNS {#routing-and-dns}

| اصطلاح | معنی | بیشتر |
| --- | --- | --- |
| Routing profile (نمایهٔ مسیریابی) | مجموعه‌ای نام‌دار از قوانین به‌همراه یک خروجی پیش‌فرض؛ در هر زمان یکی از آن‌ها فعال است. | [مسیریابی](@/guides/routing.fa.md#profiles) |
| Rule (قانون) | یک شرط، مانند یک دامنه یا یک برنامه، به‌همراه خروجی اتصال‌های منطبق. | [مسیریابی](@/guides/routing.fa.md#advanced-rules) |
| Rule-set (مجموعه‌قوانین) | فهرستی آماده از دامنه‌ها (`geosite-…`) یا محدوده‌های IP (`geoip-…`) برای استفاده در قوانین. | [مجموعه‌قوانین](@/guides/routing.fa.md#rule-sets) |
| Default outbound (خروجی پیش‌فرض) | جایی که اتصال، وقتی هیچ قانونی منطبق نباشد، به آن می‌رود. | [مسیریابی](@/guides/routing.fa.md#how-routing-works) |
| warp-bypass | خروجی‌ای که از نمایهٔ شما استفاده می‌کند اما از WARP عبور نمی‌کند. | [Cloudflare WARP](@/advanced/warp.fa.md#warp-bypass) |
| Remote routing profile (نمایهٔ مسیریابی راه‌دور) | نمایهٔ مسیریابی‌ای که Throne از یک URL دانلود می‌کند. | [نمایه‌های راه‌دور](@/guides/routing.fa.md#remote-profiles) |
| Sniffing (پویش) | خواندن نام دامنه از ابتدای اتصال، تا قوانین دامنه برای اتصال به نشانی‌های IP هم کار کنند. | [مسیریابی](@/guides/routing.fa.md#advanced-rules) |
| DNS routing (مسیریابی DNS) | گزینهٔ `Enable DNS Routing`، که دامنه‌های منطبق با قوانین `direct` را با DNS مستقیم تفکیک می‌کند. | [DNS](@/guides/dns.fa.md#how-dns-works) |
| Remote DNS / direct DNS (DNS سمت‌سرور / DNS مستقیم) | سرورهای DNS که از طریق پروکسی (سمت‌سرور) و بدون آن (مستقیم) استفاده می‌شوند. | [تنظیمات DNS](@/guides/dns.fa.md#settings) |
| FakeIP (نشانی IP جعلی) | حالتی از DNS که با نشانی‌های جعلی موقت پاسخ می‌دهد و آن‌ها را دوباره به نام‌های دامنهٔ واقعی نگاشت می‌کند. | [FakeIP](@/guides/dns.fa.md#fakeip) |

## پروتکل‌ها و ضدسانسور {#protocols}

| اصطلاح | معنی | بیشتر |
| --- | --- | --- |
| Reality | یک گزینهٔ امنیتی برای VLESS که اتصال را شبیه بازدید از یک وب‌سایت واقعی و نامرتبط نشان می‌دهد. | [sing-box در برابر Xray](@/advanced/xray.fa.md#reality) |
| XHTTP | یک ترنسپورت Xray که اتصال را درون درخواست‌های معمولی HTTP منتقل می‌کند. | [sing-box در برابر Xray](@/advanced/xray.fa.md#vless-preference) |
| uTLS | دست‌دهی (handshake) TLS را شبیه دست‌دهی یک مرورگر رایج می‌کند. | [پیش‌تنظیم‌های ضدسانسور](@/advanced/presets.fa.md#utls) |
| ECH | Encrypted Client Hello، که نام سرور را درون دست‌دهی TLS پنهان می‌کند. | [پیش‌تنظیم‌های ضدسانسور](@/advanced/presets.fa.md#ech) |
| TLS fragment (قطعه‌بندی TLS) | نخستین پیام TLS را به قطعه‌های کوچک تقسیم می‌کند تا از برخی فیلترهای سانسور عبور کند. | [پیش‌تنظیم‌های ضدسانسور](@/advanced/presets.fa.md#tls-fragment) |
| Multiplex (mux) (چندگانه‌سازی) | چندین اتصال را درون یک اتصال به سرور منتقل می‌کند. | [پیش‌تنظیم‌های ضدسانسور](@/advanced/presets.fa.md#multiplex) |
| WARP | سرویس VPN رایگان Cloudflare که Throne می‌تواند آن را به‌عنوان آخرین هاپ پس از نمایهٔ شما اضافه کند. | [Cloudflare WARP](@/advanced/warp.fa.md) |
| MASQUE | یک پروتکل تونل روی HTTP/3 یا HTTP/2 که به‌صورت یک نوع نمایه و یکی از حالت‌های WARP در دسترس است. | [Cloudflare WARP](@/advanced/warp.fa.md) |
| DNS tunnel (تونل DNS) | ترافیک شما را درون پرس‌وجوهای DNS حمل می‌کند، برای شبکه‌هایی که فقط DNS از آن‌ها عبور می‌کند. | [تونل‌های DNS](@/advanced/dns_tunnels.fa.md) |
| Resolver (ریزالور) | سرور DNS که به‌جای شما به پرس‌وجوها پاسخ می‌دهد و در تونل DNS آن‌ها را به سرور تونل می‌رساند. | [تونل‌های DNS](@/advanced/dns_tunnels.fa.md#resolvers) |
| mKCP | یک ترنسپورت Xray که اتصال را روی UDP حمل می‌کند. | [تونل‌های DNS](@/advanced/dns_tunnels.fa.md#xdns) |
| XDNS | یک تونل DNS در نمایهٔ `VLESS (Xray)` که بسته‌های mKCP را به‌صورت پرس‌وجوهای DNS می‌فرستد. | [تونل‌های DNS](@/advanced/dns_tunnels.fa.md#xdns) |

## حریم خصوصی، فایل‌ها و امنیت {#files-and-security}

| اصطلاح | معنی | بیشتر |
| --- | --- | --- |
| HWID (شناسهٔ سخت‌افزاری) | یک شناسهٔ سخت‌افزاری که برخی ارائه‌دهندگان اشتراک درخواست می‌کنند. | [حریم خصوصی و درخواست‌های شبکه](@/reference/privacy.fa.md#hwid) |
| OTP (گذرواژهٔ یک‌بارمصرف) | گذرواژه‌های یک‌بارمصرف (کدهای TOTP یا HOTP) که برخی سرورهای OpenVPN و OpenConnect درخواست می‌کنند. | [مدیر OTP](@/advanced/vpn_profiles.fa.md#otp-manager) |
| .thrbackup | فایل پشتیبان Throne، در دسکتاپ و در Android. | [پشتیبان‌گیری و بازیابی](@/guides/backup.fa.md#backup-restore) |
| Portable mode (حالت قابل‌حمل) | Throne داده‌هایش را به‌جای پوشهٔ کاربری شما، کنار برنامه نگه می‌دارد. | [پوشهٔ داده‌ها](@/reference/files.fa.md#data-folder) |
| Kill switch (کیل‌سوییچ) | تنظیمی که همهٔ ترافیکی را که از Throne عبور نمی‌کند مسدود می‌کند. | [کیل‌سوییچ (Kill Switch)](@/advanced/kill_switch.fa.md) |

## خودکارسازی و حالت بدون رابط گرافیکی {#automation}

| اصطلاح | معنی | بیشتر |
| --- | --- | --- |
| Throne API | رابطی رمزگذاری‌شده که اسکریپت‌ها و دستگاه‌های دیگر از طریق آن و با یک کلید مخفی Throne را کنترل می‌کنند. | [Throne API و thronectl](@/advanced/api.fa.md) |
| thronectl | کلاینت خط فرمان Throne API. | [Throne API و thronectl](@/advanced/api.fa.md#commands) |
| Admin request (درخواست مدیریتی) | درخواستی حساس از Throne API که Throne فقط از رایانهٔ خودش به آن پاسخ می‌دهد، مگر اینکه شبکهٔ محلی را مجاز کنید. | [درخواست‌های مدیریتی](@/advanced/api.fa.md#admin-requests) |
| Headless Throne (Throne بدون رابط گرافیکی) | Throne بدون پنجره، برای سرورها، روترها و کانتینرها. | [Throne بدون رابط گرافیکی (Headless)](@/headless/_index.fa.md) |

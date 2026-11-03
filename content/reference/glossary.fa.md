+++
title = "واژه‌نامه"
description = "توضیح کوتاه اصطلاحاتی که در Throne با آن‌ها روبه‌رو می‌شوید، از نمایه‌ها و مسیریابی تا حالت TUN، DNS، گزینه‌های ضدسانسور و Throne API."
weight = 50
toc = true
+++

این صفحه اصطلاحاتی را که در Throne با آن‌ها روبه‌رو می‌شوید، هر کدام در یک جمله توضیح می‌دهد. برای اطلاعات بیشتر، لینک ستون آخر را دنبال کنید.

## نمایه‌ها و گروه‌ها {#profiles-and-groups}

| اصطلاح | معنی | بیشتر |
| --- | --- | --- |
| Profile (نمایه) | یک سرور یا پروکسی در فهرست، همراه با پروتکل، نشانی و تنظیمات آن. | [شروع سریع](@/get_started/configuration.fa.md#add-servers) |
| Group (گروه) | زبانه‌ای در پنجرهٔ اصلی که نمایه‌ها را در خود دارد؛ یک گروه یا از نوع `پایه` (Basic) است (نمایه‌ها را خودتان اضافه می‌کنید) یا از نوع `اشتراک` (Subscription) (از یک URL پر می‌شود). | [اشتراک‌ها و گروه‌ها](@/guides/subscriptions.fa.md#group-options) |
| Subscription (اشتراک) | یک URL از ارائه‌دهندهٔ شما که فهرستی از نمایه‌ها را برمی‌گرداند؛ Throne این نمایه‌ها را در یک گروه دانلود می‌کند و بعداً می‌تواند آن‌ها را به‌روزرسانی کند. | [اشتراک‌ها و گروه‌ها](@/guides/subscriptions.fa.md#add-subscription) |
| Share link (لینک اشتراک‌گذاری) | یک لینک تک‌خطی برای یک نمایه، مانند `vless://…` یا `ss://…`، که برنامه‌های دیگر هم آن را می‌فهمند. | [پروتکل‌ها و قالب‌های واردکردن](@/reference/protocols.fa.md#share-links) |
| Deep link (لینک عمیق) | یک لینک `throne://` که به Throne می‌گوید کاری انجام دهد، مانند افزودن یک اشتراک یا یک نمایهٔ مسیریابی. | [لینک‌های عمیق](@/advanced/deeplinks.fa.md#format) |
| Throne link (لینک Throne) | یک لینک عمیق `throne://add/…` که یک نمایه را در خود دارد؛ آن را با `Copy links of selected (Deep Links)` به دست می‌آورید (فقط در حالت پیشرفته). | [لینک‌های عمیق](@/advanced/deeplinks.fa.md#add) |
| Chain (زنجیره) | نمایه‌ای از نوع `پروکسی زنجیره ای` (Chain Proxy) که ترافیک را به‌ترتیب از چند نمایه عبور می‌دهد. | [زنجیره‌های پروکسی و پیکربندی‌های سفارشی](@/advanced/chains.fa.md#chains) |
| Front / landing proxy (پروکسی front / پروکسی مقصد) | گزینه‌هایی در گروه که یک نمایهٔ اضافه را پیش از (front) یا پس از (landing) هر نمایهٔ گروه قرار می‌دهند (فقط در حالت پیشرفته). | [زنجیره‌های پروکسی و پیکربندی‌های سفارشی](@/advanced/chains.fa.md#front-landing) |
| Auto selector (انتخابگر خودکار) | نوعی نمایه که سرورهای یک گروه را تست می‌کند و به‌طور خودکار از بهترین‌ها استفاده می‌کند. | [تست و انتخابگر خودکار](@/guides/testing.fa.md#auto-selector) |
| URL test (تست URL) | یک تست تأخیر که `آدرس تست تاخیر` (Latency Test URL) را از طریق یک نمایه باز می‌کند و مدت زمان آن را اندازه می‌گیرد. | [تست و انتخابگر خودکار](@/guides/testing.fa.md#url-test) |

## رابط کاربری {#interface}

| اصطلاح | معنی | بیشتر |
| --- | --- | --- |
| Simple mode (حالت ساده) | حالتی از رابط کاربری که نصب تازه از نسخهٔ 1.4.0 با آن شروع می‌شود؛ گزینه‌های پیشرفتهٔ منوها و تنظیمات را پنهان می‌کند، و این تنظیمات مقدار خود را نگه می‌دارند و همچنان اعمال می‌شوند. | [حالت ساده و پیشرفته](@/get_started/configuration.fa.md#interface-mode) |
| Advanced mode (حالت پیشرفته) | حالتی از رابط کاربری که همهٔ گزینه‌های منوها و تنظیمات را نشان می‌دهد؛ نصب‌هایی که از نسخه‌ای قدیمی‌تر به‌روزرسانی شده‌اند همین حالت را نگه می‌دارند. | [حالت ساده و پیشرفته](@/get_started/configuration.fa.md#interface-mode) |
| Command palette (پالت فرمان) | پنجره‌ای برای جست‌وجو که با `Ctrl+K` باز می‌شود، فرمان‌های منوها و تنظیمات را با نامشان پیدا می‌کند و آن‌ها را اجرا می‌کند یا باز می‌کند. | [پالت فرمان](@/reference/shortcuts.fa.md#command-palette) |

## هسته و حالت‌های اتصال {#core-and-modes}

| اصطلاح | معنی | بیشتر |
| --- | --- | --- |
| Core (هسته) | ThroneCore، برنامهٔ پس‌زمینه‌ای که ترافیک شما را منتقل می‌کند؛ پنجرهٔ Throne فقط آن را کنترل می‌کند. | [Throne چیست](@/get_started/_index.fa.md#what-is-throne) |
| sing-box | موتور متن‌بازی که ThroneCore بر پایهٔ آن ساخته شده است؛ حالت TUN، مسیریابی، DNS و بیشتر پروتکل‌ها را مدیریت می‌کند. | [sing-box در برابر Xray](@/advanced/xray.fa.md) |
| Xray | موتور دومی که درون هسته، برای نمایه‌های `VLESS (Xray)` و پیکربندی‌های Xray اجرا می‌شود. | [sing-box در برابر Xray](@/advanced/xray.fa.md#vless-preference) |
| Inbound (ورودی) | راهی برای ورود ترافیک به Throne: پورت mixed، آداپتور TUN یا یک ورودی سفارشی. | [تنظیمات ورودی](@/guides/proxy_modes.fa.md#inbound-settings) |
| Outbound (خروجی) | راهی برای خروج ترافیک از Throne: از طریق یک نمایه (proxy)، به‌صورت مستقیم (direct)، یا اصلاً خارج نشود (block). | [مسیریابی](@/guides/routing.fa.md#how-routing-works) |
| Mixed port (پورت mixed) | پورت محلی که هم اتصال‌های پروکسی SOCKS5 و هم HTTP را می‌پذیرد؛ به‌طور پیش‌فرض `127.0.0.1:2080`. | [تنظیمات ورودی](@/guides/proxy_modes.fa.md#inbound-settings) |
| System proxy (پروکسی سیستمی) | حالت `پروکسی سیستمی` (System Proxy) که تنظیم پروکسی سیستم شما را به پورت mixed هدایت می‌کند تا برنامه‌هایی که از این تنظیم پیروی می‌کنند از Throne استفاده کنند. | [پروکسی سیستمی](@/guides/proxy_modes.fa.md#system-proxy) |
| TUN (حالت TUN) | گزینهٔ `حالتvpn` (Tun Mode) که یک آداپتور شبکهٔ مجازی می‌سازد تا ترافیک همهٔ برنامه‌ها از Throne عبور کند. | [حالت TUN](@/guides/tun_mode.fa.md) |
| Stack (پشته) | نحوهٔ پردازش بسته‌ها در حالت TUN؛ نسخه‌های قدیمی‌تر اجازه می‌دادند `system`، `gvisor` یا `mixed` را انتخاب کنید، اما Throne 1.4.0 و Throne برای Android 2.0.2 چنین تنظیمی ندارند. | [تنظیمات TUN](@/guides/tun_mode.fa.md#settings) |
| Strict route (مسیر سخت‌گیرانه) | گزینه‌ای در حالت TUN که مانع می‌شود ترافیک تونل را دور بزند؛ در Windows 10 و نسخه‌های بعدی به‌طور پیش‌فرض روشن است. | [محافظت از نشت DNS در Windows](@/advanced/windows_tun_mode.fa.md#strict-route) |
| MTU | بزرگ‌ترین اندازهٔ بسته در آداپتور TUN: به‌طور پیش‌فرض 1500 در دسکتاپ و 9000 در Android. | [تنظیمات TUN](@/guides/tun_mode.fa.md#settings) |

## مسیریابی و DNS {#routing-and-dns}

| اصطلاح | معنی | بیشتر |
| --- | --- | --- |
| Routing profile (نمایهٔ مسیریابی) | مجموعه‌ای نام‌دار از قوانین به‌همراه یک خروجی پیش‌فرض؛ در هر زمان دقیقاً یک نمایهٔ مسیریابی فعال است. | [مسیریابی](@/guides/routing.fa.md#profiles) |
| Rule (قانون) | یک شرط، مانند یک دامنه، یک محدودهٔ IP، یک مجموعه‌قوانین یا یک برنامه، به‌همراه کاری که باید با اتصال‌های منطبق انجام شود؛ قوانین از بالا به پایین بررسی می‌شوند. | [مسیریابی](@/guides/routing.fa.md#advanced-rules) |
| Rule-set (مجموعه‌قوانین) | فهرستی آماده از دامنه‌ها (`geosite-…`) یا محدوده‌های IP (`geoip-…`) برای استفاده در قوانین، که به‌صورت فایل باینری `.srs` دانلود می‌شود. | [مجموعه‌قوانین](@/guides/routing.fa.md#rule-sets) |
| Default outbound (خروجی پیش‌فرض) | جایی که اتصال، وقتی هیچ قانونی منطبق نباشد، به آن می‌رود: `proxy`، `direct`، `block` یا `warp-bypass`. | [مسیریابی](@/guides/routing.fa.md#how-routing-works) |
| Direct (مستقیم) | اتصال بدون پروکسی و از طریق اتصال معمولی شما به اینترنت خارج می‌شود. | [مسیریابی](@/guides/routing.fa.md#how-routing-works) |
| Block (مسدود) | اتصال رد می‌شود. | [مسیریابی](@/guides/routing.fa.md#how-routing-works) |
| warp-bypass | وقتی WARP روشن است، اتصال از طریق نمایهٔ شما می‌رود اما از WARP عبور نمی‌کند. | [Cloudflare WARP](@/advanced/warp.fa.md#warp-bypass) |
| Remote routing profile (نمایهٔ مسیریابی راه‌دور) | نمایهٔ مسیریابی‌ای که Throne از یک URL دانلود می‌کند و می‌تواند آن را به‌طور خودکار به‌روزرسانی کند. | [نمایه‌های راه‌دور](@/guides/routing.fa.md#remote-profiles) |
| Sniffing (پویش) | Throne نام دامنه را از ابتدای اتصال می‌خواند تا قوانین دامنه حتی وقتی برنامه‌ای به یک نشانی IP وصل می‌شود هم کار کنند. | [مسیریابی](@/guides/routing.fa.md#advanced-rules) |
| DNS routing (مسیریابی DNS) | گزینهٔ `Enable DNS Routing`: دامنه‌هایی که با قوانین `direct` شما منطبق‌اند با DNS مستقیم تفکیک می‌شوند و همهٔ پرس‌وجوهای دیگر به `سرور دی‌ان‌اس پیش‌فرض` (Default DNS server) می‌روند (که به‌طور پیش‌فرض DNS سمت‌سرور است). | [DNS](@/guides/dns.fa.md#how-dns-works) |
| Remote DNS / direct DNS (DNS سمت‌سرور / DNS مستقیم) | DNS سمت‌سرور (به‌طور پیش‌فرض `https://8.8.8.8/dns-query`) از طریق پروکسی استفاده می‌شود و سرور پیش‌فرض است؛ DNS مستقیم (به‌طور پیش‌فرض `localhost`، یعنی حل‌کنندهٔ DNS سیستم‌عامل شما) بدون پروکسی استفاده می‌شود، به دامنه‌هایی که با قوانین `direct` منطبق‌اند پاسخ می‌دهد و نشانی سرورهای شما را پیدا می‌کند. | [تنظیمات DNS](@/guides/dns.fa.md#settings) |
| FakeIP (نشانی IP جعلی) | حالتی از DNS، به‌طور پیش‌فرض خاموش، که با نشانی‌های جعلی موقت پاسخ می‌دهد و آن‌ها را دوباره به نام‌های دامنهٔ واقعی نگاشت می‌کند. | [FakeIP](@/guides/dns.fa.md#fakeip) |

## پروتکل‌ها و ضدسانسور {#protocols}

| اصطلاح | معنی | بیشتر |
| --- | --- | --- |
| Reality | یک گزینهٔ امنیتی برای VLESS که اتصال را شبیه بازدید از یک وب‌سایت واقعی و نامرتبط نشان می‌دهد. | [sing-box در برابر Xray](@/advanced/xray.fa.md#reality) |
| XHTTP | یک ترنسپورت Xray که اتصال را درون درخواست‌های معمولی HTTP منتقل می‌کند. | [sing-box در برابر Xray](@/advanced/xray.fa.md#vless-preference) |
| uTLS | دست‌دهی (handshake) TLS را شبیه دست‌دهی یک مرورگر رایج، مانند Chrome، می‌کند. | [پیش‌تنظیم‌های ضدسانسور](@/advanced/presets.fa.md#utls) |
| ECH | Encrypted Client Hello، که نام سرور را درون دست‌دهی TLS پنهان می‌کند. | [پیش‌تنظیم‌های ضدسانسور](@/advanced/presets.fa.md#ech) |
| TLS fragment (قطعه‌بندی TLS) | نخستین پیام TLS (یعنی Client Hello) را به قطعه‌های کوچک تقسیم می‌کند تا از برخی فیلترهای سانسور عبور کند. | [پیش‌تنظیم‌های ضدسانسور](@/advanced/presets.fa.md#tls-fragment) |
| Multiplex (mux) (چندگانه‌سازی) | چندین اتصال را درون یک اتصال به سرور منتقل می‌کند، با smux، h2mux یا yamux. | [پیش‌تنظیم‌های ضدسانسور](@/advanced/presets.fa.md#multiplex) |
| WARP | سرویس VPN رایگان Cloudflare که Throne می‌تواند آن را به‌عنوان آخرین هاپ پس از نمایهٔ شما اضافه کند. | [Cloudflare WARP](@/advanced/warp.fa.md) |
| MASQUE | یک پروتکل تونل روی HTTP/3 یا HTTP/2؛ Throne یک نوع نمایهٔ `MASQUE` دارد و WARP هم می‌تواند از آن استفاده کند. | [Cloudflare WARP](@/advanced/warp.fa.md) |
| DNS tunnel (تونل DNS) | ترافیک شما را درون پرس‌وجوهای DNS حمل می‌کند، برای شبکه‌هایی که فقط DNS از آن‌ها عبور می‌کند. Throne نوع‌های نمایهٔ `DNSTT / VayDNS` و `MasterDNS` و همچنین XDNS را دارد. | [تونل‌های DNS](@/advanced/dns_tunnels.fa.md) |
| Resolver (ریزالور) | سرور DNS که به‌جای شما به پرس‌وجوهای DNS پاسخ می‌دهد. تونل DNS پرس‌وجوهایش را به ریزالورها می‌فرستد و آن‌ها پرس‌وجوها را به سرور تونل می‌رسانند. | [تونل‌های DNS](@/advanced/dns_tunnels.fa.md#resolvers) |
| mKCP | یک ترنسپورت Xray که اتصال را روی UDP حمل می‌کند. | [تونل‌های DNS](@/advanced/dns_tunnels.fa.md#xdns) |
| XDNS | mKCP با یک ماسک `xdns` در Finalmask که بسته‌های mKCP را به‌صورت پرس‌وجوهای DNS می‌فرستد: یک تونل DNS در نمایهٔ `VLESS (Xray)`. | [تونل‌های DNS](@/advanced/dns_tunnels.fa.md#xdns) |

## حریم خصوصی، فایل‌ها و امنیت {#files-and-security}

| اصطلاح | معنی | بیشتر |
| --- | --- | --- |
| HWID (شناسهٔ سخت‌افزاری) | یک شناسهٔ سخت‌افزاری که برخی ارائه‌دهندگان درخواست می‌کنند؛ Throne فقط اگر آن را روشن کنید، ارسالش می‌کند. | [حریم خصوصی و درخواست‌های شبکه](@/reference/privacy.fa.md#hwid) |
| OTP (گذرواژهٔ یک‌بارمصرف) | گذرواژه‌های یک‌بارمصرف (کدهای TOTP یا HOTP) که برخی سرورهای OpenVPN و OpenConnect درخواست می‌کنند؛ Throne آن‌ها را در `Tools` → `OTP Manager` نگه می‌دارد (فقط در حالت پیشرفته). | [مدیر OTP](@/advanced/vpn_profiles.fa.md#otp-manager) |
| .thrbackup | فایل پشتیبان Throne؛ هم برنامهٔ دسکتاپ و هم برنامهٔ Android می‌توانند آن را بسازند و بازیابی کنند. | [پشتیبان‌گیری و بازیابی](@/guides/backup.fa.md#backup-restore) |
| Portable mode (حالت قابل‌حمل) | Throne داده‌هایش را به‌جای پوشهٔ کاربری شما، در یک پوشهٔ `config` کنار برنامه نگه می‌دارد. | [پوشهٔ داده‌ها](@/reference/files.fa.md#data-folder) |
| Kill switch (کیل‌سوییچ) | تنظیمی که همهٔ ترافیکی را که از Throne عبور نمی‌کند مسدود می‌کند، حتی وقتی هیچ نمایه‌ای در حال اجرا نیست (از نسخهٔ 1.4.0). | [کیل‌سوییچ (Kill Switch)](@/advanced/kill_switch.fa.md) |

## خودکارسازی و حالت بدون رابط گرافیکی {#automation}

| اصطلاح | معنی | بیشتر |
| --- | --- | --- |
| Throne API | رابطی رمزگذاری‌شده، که در برنامهٔ دسکتاپ به‌طور پیش‌فرض خاموش است و اسکریپت‌ها و دستگاه‌های دیگر از طریق آن و با یک کلید مخفی Throne را کنترل و مدیریت می‌کنند (از نسخهٔ 1.4.0). | [Throne API و thronectl](@/advanced/api.fa.md) |
| thronectl | کلاینت خط فرمان Throne API، برای ترمینال‌ها، اسکریپت‌ها و ابزارهای کلید میانبر. | [Throne API و thronectl](@/advanced/api.fa.md#commands) |
| Admin request (درخواست مدیریتی) | درخواستی از Throne API که وارد می‌کند، حذف می‌کند، تنظیمات را تغییر می‌دهد، پشتیبان می‌گیرد، بازیابی می‌کند، Throne را دوباره راه‌اندازی می‌کند یا از آن خارج می‌شود؛ Throne فقط از رایانهٔ خودش به آن پاسخ می‌دهد، مگر اینکه شبکهٔ محلی را مجاز کنید. | [درخواست‌های مدیریتی](@/advanced/api.fa.md#admin-requests) |
| Headless Throne (Throne بدون رابط گرافیکی) | Throne که با `--headless` اجرا شده است: بدون پنجره، آیکون سینی سیستم یا کادر گفت‌وگو، برای سرورها، روترها و کانتینرها، و با thronectl کنترل می‌شود (از نسخهٔ 1.4.0). | [Throne بدون رابط گرافیکی (Headless)](@/headless/_index.fa.md) |

+++
title = "شروع کار"
description = "Throne چیست، روی کدام سیستم‌ها اجرا می‌شود و از کجا باید شروع کرد."
weight = 1
sort_by = "weight"
aliases = ["/fa/introduction/"]
+++

این بخش توضیح می‌دهد که Throne چیست، از کدام سیستم‌ها پشتیبانی می‌کند و کدام صفحه‌ها را باید اول بخوانید. اگر با Throne تازه آشنا شده‌اید، از همین‌جا شروع کنید.

## Throne چیست {#what-is-throne}

Throne (پیش‌تر با نام Nekoray) یک کلاینت پروکسی رایگان و متن‌باز (GPL-3.0) برای Windows، Linux و macOS است. رابط گرافیکی آن با Qt ساخته شده است و از ThroneCore استفاده می‌کند؛ هسته‌ای مبتنی بر sing-box که برای نمایه‌هایی که به Xray نیاز دارند، Xray را هم اجرا می‌کند. سرورها را از ارائه‌دهندهٔ خود یا از سرور شخصی‌تان اضافه می‌کنید، تعیین می‌کنید کدام ترافیک از آن‌ها عبور کند و در حالت پروکسی سیستمی یا حالت TUN متصل می‌شوید.

Throne برای Android (پیش‌تر با نام NekoBox for Android) برنامهٔ Android آن است. این برنامه از نسخهٔ 2.0.0 همان هسته، همان ساختار تنظیمات و همان قالب پشتیبان‌گیری نسخهٔ دسکتاپ را به کار می‌برد، بنابراین می‌توانید تنظیمات خود را بین رایانه و گوشی جابه‌جا کنید. [Throne برای Android](@/android/_index.fa.md) را ببینید.

از نسخهٔ 1.4.0، همین برنامهٔ Throne بدون پنجره هم اجرا می‌شود، روی یک سرور، یک روتر یا در یک کانتینر، و آن را با ابزار خط فرمان `thronectl` کنترل می‌کنید. [Throne بدون رابط گرافیکی](@/headless/_index.fa.md) را ببینید.

اگر پیش‌تر از Nekoray یا NekoBox استفاده می‌کردید، [مهاجرت از Nekoray / NekoBox](@/help/migrating.fa.md) را بخوانید.

## پلتفرم‌ها {#platforms}

| سیستم | پیش‌نیازها | انواع بسته |
|---|---|---|
| Windows | Windows 10 1809 یا جدیدتر (x64، ARM64). Windows 7 SP1 یا جدیدتر با بیلدهای legacy (x64، x86 32 بیتی). | نصب‌کننده، ZIP قابل حمل |
| Linux | x64 یا ARM64، glibc 2.28 یا جدیدتر، برای نمونه Debian 10، Ubuntu 20.04 یا RHEL 8 (از نسخهٔ 1.4.0). بسته‌های `-system-qt` به glibc 2.34 یا جدیدتر نیاز دارند. | ZIP قابل حمل، `.deb`، `.rpm`، اسکریپت نصب، [مخزن‌های APT و RPM](@/get_started/installation.fa.md#apt-repository)، [AUR](@/get_started/installation.fa.md#aur) |
| macOS | macOS 14.4 یا جدیدتر (Apple Silicon و Intel). macOS 10.15 یا جدیدتر با بیلد legacy (Intel). | ZIP، [Homebrew](@/get_started/installation.fa.md#homebrew) |
| Android | Android 7.0 یا جدیدتر | APK |

فایل‌ها را از صفحهٔ [دانلود](@/downloads.fa.md) دریافت کنید.

## ویژگی‌های برجسته {#highlights}

- پروتکل‌های متعدد، از جمله VLESS با REALITY و XHTTP، Shadowsocks، Trojan، Hysteria، TUIC، WireGuard و AmneziaWG، NaïveProxy، OpenVPN و OpenConnect: [پروتکل‌ها و قالب‌های واردکردن](@/reference/protocols.fa.md).
- تونل‌های DNS، که ترافیک شما را درون درخواست‌های DNS جابه‌جا می‌کنند: DNSTT/VayDNS، MasterDNS و XDNS برای VLESS (Xray) (از نسخهٔ 1.4.0): [تونل‌های DNS](@/advanced/dns_tunnels.fa.md).
- اشتراک‌هایی که طبق زمان‌بندی به‌روز می‌شوند، با تنظیمات User-Agent و HWID برای هر گروه: [اشتراک‌ها و گروه‌ها](@/guides/subscriptions.fa.md).
- پروکسی سیستمی یا حالت TUN برای همهٔ برنامه‌ها، و اشتراک‌گذاری پروکسی با دستگاه‌های دیگر شبکهٔ شما: [پروکسی سیستمی، TUN و اشتراک‌گذاری در شبکهٔ محلی](@/guides/proxy_modes.fa.md).
- نمایه‌های مسیریابی با قوانین و مجموعه‌قوانین (rule-set)، و نمایه‌های آماده برای چین، ایران و روسیه: [مسیریابی](@/guides/routing.fa.md).
- تست تأخیر، IP و سرعت، و یک انتخابگر خودکار (Auto Selector) که شما را روی سروری نگه می‌دارد که کار می‌کند: [تست و انتخابگر خودکار](@/guides/testing.fa.md).
- زنجیره‌های پروکسی و پیکربندی‌های sing-box یا Xray خودتان: [زنجیره‌های پروکسی و پیکربندی‌های سفارشی](@/advanced/chains.fa.md).
- گزینه‌های ضدسانسور مانند TLS fragment، uTLS و ECH: [پیش‌تنظیم‌های ضدسانسور](@/advanced/presets.fa.md).
- Cloudflare WARP داخلی: [Cloudflare WARP](@/advanced/warp.fa.md).
- پشتیبان‌هایی که هم در نسخهٔ دسکتاپ و هم در برنامهٔ Android کار می‌کنند: [پشتیبان‌گیری، به‌روزرسانی و مهاجرت](@/guides/backup.fa.md).
- کیل‌سوییچی که ترافیک خارج از Throne را مسدود می‌کند (از نسخهٔ 1.4.0): [کیل‌سوییچ (Kill Switch)](@/advanced/kill_switch.fa.md).
- Throne API، برای کنترل و مدیریت Throne از اسکریپت‌ها، کلیدهای میانبر و دستگاه‌های دیگر با `thronectl` (از نسخهٔ 1.4.0): [Throne API و thronectl](@/advanced/api.fa.md).
- حالت بدون رابط گرافیکی (headless) برای سرورها و کانتینرها (از نسخهٔ 1.4.0): [Throne بدون رابط گرافیکی (Headless)](@/headless/_index.fa.md).
- حالت ساده‌ای که گزینه‌های پیشرفته را برای کاربران تازه‌کار پنهان می‌کند، و جست‌وجوی فرمان‌ها و تنظیمات با `Ctrl+K` (از نسخهٔ 1.4.0): [حالت ساده و پیشرفته](@/get_started/configuration.fa.md#interface-mode).

## از کجا شروع کنیم {#where-to-start}

1. Throne را نصب کنید: [نصب](@/get_started/installation.fa.md).
2. سرورهای خود را اضافه کنید و متصل شوید: [شروع سریع](@/get_started/configuration.fa.md).
3. برای قابلیت‌هایی که نیاز دارید، مانند اشتراک‌ها، مسیریابی و حالت TUN، [راهنماها](@/guides/_index.fa.md) را بخوانید.

در Android، با [Throne برای Android](@/android/_index.fa.md) شروع کنید. اگر چیزی کار نمی‌کند، [عیب‌یابی](@/help/troubleshooting.fa.md) و [پرسش‌های متداول](@/help/faq.fa.md) را ببینید.

## قدردانی {#credits}

Throne بر پایهٔ این پروژه‌ها ساخته شده است:

- [SagerNet/sing-box](https://github.com/SagerNet/sing-box): موتور پروکسی هسته
- [XTLS/Xray-core](https://github.com/xtls/xray-core): موتور Xray
- [Qv2ray](https://github.com/Qv2ray/Qv2ray): بخش‌هایی از رابط کاربری و کد پروکسی سیستمی
- [Qt](https://www.qt.io/): جعبه‌ابزار رابط کاربری
- [simple-protobuf](https://github.com/tonda-kriz/simple-protobuf): پیام‌های میان رابط کاربری و هسته
- [fkYAML](https://github.com/fktn-k/fkYAML): وارد کردن فایل‌های YAML مربوط به Clash
- [quirc](https://github.com/dlbeer/quirc): خواندن کد QR
- [srombauts/sqlitecpp](https://github.com/srombauts/sqlitecpp): پایگاه‌دادهٔ نمایه‌ها و تنظیمات

برای حمایت از توسعهٔ Throne، در `برنامه` (Program) ← `درباره` (About) روی `حمایت از Throne` (Support Throne) کلیک کنید (از نسخهٔ 1.4.0). این دکمه صفحهٔ کمک مالی را باز می‌کند. Throne برای Android همین گزینه را در صفحهٔ `درباره برنامه` (About) خود دارد (از نسخهٔ 2.0.4).

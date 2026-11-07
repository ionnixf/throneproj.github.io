+++
title = "شروع کار"
description = "Throne چیست، روی کدام سیستم‌ها اجرا می‌شود و از کجا باید شروع کرد."
weight = 1
sort_by = "weight"
aliases = ["/fa/introduction/"]
+++

## Throne چیست {#what-is-throne}

Throne (پیش‌تر با نام Nekoray) یک کلاینت پروکسی رایگان و متن‌باز (GPL-3.0) برای Windows، Linux و macOS است. هستهٔ آن، ThroneCore، مبتنی بر sing-box است و برای نمایه‌هایی که به Xray نیاز دارند، Xray را هم اجرا می‌کند. سرورها را اضافه می‌کنید، تعیین می‌کنید کدام ترافیک از آن‌ها عبور کند و در حالت پروکسی سیستمی یا حالت TUN متصل می‌شوید.

- [Throne برای Android](@/android/_index.fa.md) (پیش‌تر با نام NekoBox for Android) همان هسته، همان تنظیمات و همان [قالب پشتیبان‌گیری](@/guides/backup.fa.md) نسخهٔ دسکتاپ را به کار می‌برد، بنابراین می‌توانید تنظیمات خود را بین رایانه و گوشی جابه‌جا کنید.
- [Throne بدون رابط گرافیکی](@/headless/_index.fa.md) بدون پنجره روی یک سرور، یک روتر یا در یک کانتینر اجرا می‌شود و با `thronectl` کنترل می‌شود.
- اگر پیش‌تر از Nekoray یا NekoBox استفاده می‌کردید، [مهاجرت از Nekoray / NekoBox](@/help/migrating.fa.md) را بخوانید.

## پلتفرم‌ها {#platforms}

| سیستم | پیش‌نیازها | انواع بسته |
|---|---|---|
| Windows | Windows 10 1809 یا جدیدتر (x64، ARM64). Windows 7 SP1 یا جدیدتر با بیلدهای legacy (x64، x86 32 بیتی). | نصب‌کننده، ZIP قابل حمل |
| Linux | x64 یا ARM64، glibc 2.28 یا جدیدتر، برای نمونه Debian 10، Ubuntu 20.04 یا RHEL 8. بسته‌های `-system-qt` به glibc 2.34 یا جدیدتر نیاز دارند. | ZIP قابل حمل، `.deb`، `.rpm`، اسکریپت نصب، [مخزن‌های APT و RPM](@/get_started/installation.fa.md#apt-repository)، [AUR](@/get_started/installation.fa.md#aur) |
| macOS | macOS 14.4 یا جدیدتر (Apple Silicon و Intel). macOS 10.15 یا جدیدتر با بیلد legacy (Intel). | ZIP، [Homebrew](@/get_started/installation.fa.md#homebrew) |
| Android | Android 7.0 یا جدیدتر | APK |

فایل‌ها را از صفحهٔ [دانلود](@/downloads.fa.md) دریافت کنید.

## ویژگی‌های برجسته {#highlights}

- [پروتکل‌های متعدد](@/reference/protocols.fa.md)، از جمله VLESS با REALITY و XHTTP، Shadowsocks، Trojan، Hysteria، TUIC، WireGuard، AmneziaWG، NaïveProxy، OpenVPN، OpenConnect و [تونل‌های DNS](@/advanced/dns_tunnels.fa.md).
- [اشتراک‌هایی](@/guides/subscriptions.fa.md) که طبق زمان‌بندی به‌روز می‌شوند.
- [پروکسی سیستمی، حالت TUN و اشتراک‌گذاری در شبکهٔ محلی](@/guides/proxy_modes.fa.md).
- قوانین [مسیریابی](@/guides/routing.fa.md)، با نمایه‌های آماده برای چین، ایران و روسیه.
- [تست](@/guides/testing.fa.md) تأخیر و سرعت، و یک انتخابگر خودکار (Auto Selector) که شما را روی سروری نگه می‌دارد که کار می‌کند.
- [زنجیره‌های پروکسی و پیکربندی‌های سفارشی](@/advanced/chains.fa.md)، [گزینه‌های ضدسانسور](@/advanced/presets.fa.md) مانند TLS fragment، و [Cloudflare WARP](@/advanced/warp.fa.md) داخلی.
- یک [کیل‌سوییچ](@/advanced/kill_switch.fa.md)، و [کنترل با `thronectl`](@/advanced/api.fa.md) از اسکریپت‌ها و دستگاه‌های دیگر.
- یک [حالت ساده](@/get_started/configuration.fa.md#interface-mode) برای کاربران تازه‌کار، و جست‌وجوی فرمان‌ها و تنظیمات با `Ctrl+K`.

## از کجا شروع کنیم {#where-to-start}

1. Throne را نصب کنید: [نصب](@/get_started/installation.fa.md).
2. سرورهای خود را اضافه کنید و متصل شوید: [شروع سریع](@/get_started/configuration.fa.md).
3. برای قابلیت‌هایی که نیاز دارید، [راهنماها](@/guides/_index.fa.md) را بخوانید.

در Android، با [Throne برای Android](@/android/_index.fa.md) شروع کنید. اگر چیزی کار نمی‌کند، [عیب‌یابی](@/help/troubleshooting.fa.md) و [پرسش‌های متداول](@/help/faq.fa.md) را ببینید.

## قدردانی {#credits}

Throne بر پایهٔ [sing-box](https://github.com/SagerNet/sing-box)، [Xray-core](https://github.com/xtls/xray-core)، [Qv2ray](https://github.com/Qv2ray/Qv2ray)، [Qt](https://www.qt.io/)، [simple-protobuf](https://github.com/tonda-kriz/simple-protobuf)، [fkYAML](https://github.com/fktn-k/fkYAML)، [quirc](https://github.com/dlbeer/quirc) و [SQLiteCpp](https://github.com/srombauts/sqlitecpp) ساخته شده است.

برای حمایت از Throne، در `برنامه` (Program) ← `درباره` (About) روی `حمایت از Throne` (Support Throne) کلیک کنید. در Throne برای Android این گزینه در صفحهٔ `درباره برنامه` (About) است.

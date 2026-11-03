+++
title = "مدیریت با thronectl"
description = "thronectl را به Throne بدون رابط گرافیکی متصل کنید، نمایه‌ها و اشتراک‌ها را اضافه کنید، پیکربندی دسکتاپ را با یک پشتیبان منتقل کنید، تنظیمات را تغییر دهید و Throne را از دستگاه دیگری مدیریت کنید."
weight = 20
toc = true
+++

Throne بدون رابط گرافیکی پنجره ندارد، بنابراین آن را با `thronectl`، کلاینت خط فرمان [Throne API](@/advanced/api.fa.md)، مدیریت می‌کنید. این صفحه نشان می‌دهد چگونه `thronectl` را به Throne بدون رابط گرافیکی متصل کنید و چگونه Throne را با آن راه‌اندازی کنید. [Throne API و thronectl](@/advanced/api.fa.md) فهرست کامل فرمان‌ها و پرچم‌ها (flags) را دارد. برای مدیریت Throne از مرورگر، [رابط وب](@/headless/web.fa.md) را ببینید.

## نصب thronectl {#install-thronectl}

بسته‌های `.deb` و `.rpm` برنامهٔ Throne، `thronectl` را با نام `/usr/bin/thronectl` نصب می‌کنند، و فایل ZIP مخصوص Linux آن را کنار `Throne` دارد. در جاهای دیگر، آن را روی رایانه‌ای که Throne را اجرا می‌کند نصب کنید، و روی هر رایانهٔ دیگری که می‌خواهید Throne را از آن مدیریت کنید:

- یک فایل باینری برای Linux، Windows، macOS یا FreeBSD را از [انتشارهای Throne-api](https://github.com/throneproj/Throne-api/releases) دانلود کنید، یا
- آن را با Go بسازید: `go install github.com/throneproj/Throne-api/cmd/thronectl@latest`.

[ایمیج Docker](@/headless/installation.fa.md#docker) از قبل `thronectl` را در `/usr/local/bin` دارد. فایل‌های دانلودی Throne برای Windows و macOS آن را ندارند.

برای اینکه پوسته (shell) فرمان‌ها، شناسهٔ نمایه‌ها و نام تنظیمات را کامل کند، [تکمیل خودکار در پوسته](@/advanced/api.fa.md#completion) را ببینید.

## اتصال thronectl به Throne {#connect}

`thronectl` نشانی Throne را از `THRONE_ADDR` (پیش‌فرض `127.0.0.1:9095`) می‌خواند و کلید را از فایلی که `THRONE_KEY_FILE` نام می‌برد، یا از `THRONE_KEY`، یا از `--key-file`. هرگز خود کلید را به‌عنوان آرگومان نمی‌پذیرد.

`Throne --print-connection` هر دو متغیر را به‌صورت خطوط `export` چاپ می‌کند، اما هرگز خود کلید را چاپ نمی‌کند. آن را با کاربری که Throne با آن اجرا می‌شود و با همان `--data-dir` اجرا کنید:

```bash
$ sudo /opt/Throne/Throne --print-connection
export THRONE_ADDR=127.0.0.1:9095
export THRONE_KEY_FILE=/var/lib/throne/config/api.key
```

روش استفاده از آن به نحوهٔ اجرای Throne بستگی دارد:

- **یونیت کاربری systemd** (Throne با کاربر شما اجرا می‌شود): `eval "$(/opt/Throne/Throne --print-connection)"` هر دو متغیر را تنظیم می‌کند. سپس `thronectl status` را اجرا کنید.
- **یونیت سیستمی، Docker یا LaunchDaemon در macOS** (Throne با root اجرا می‌شود): فایل کلید فقط برای root قابل خواندن است و `sudo` متغیرها را حذف می‌کند. یا کلید را در فایلی متعلق به خودتان کپی کنید:

  ```bash
  mkdir -p ~/.config/throne
  sudo install -m 600 -o "$USER" /var/lib/throne/config/api.key ~/.config/throne/api-key
  export THRONE_KEY_FILE=~/.config/throne/api-key
  thronectl status
  ```

  یا `thronectl` را با root و با فایل کلید اجرا کنید: `sudo thronectl --key-file /var/lib/throne/config/api.key status`.
- **Docker:** `thronectl` را درون کانتینر اجرا کنید: `docker compose exec throne thronectl --key-file /var/lib/throne/config/api.key status`.
- **سرویس Windows:** [اجرای فرمان‌ها از خط فرمان](@/headless/installation.fa.md#windows-prompt) را ببینید.

خط `export` را در فایل راه‌اندازی پوستهٔ خود (برای مثال `~/.bashrc`) بگذارید تا هر پوستهٔ جدیدی آن را داشته باشد.

`--print-connection` وقتی API خاموش است یا گوش نمی‌دهد، یا وقتی کلید آن در یک فایل نیست، با کد 2 خارج می‌شود. حالت آخر وقتی پیش می‌آید که پوشهٔ داده‌ها از یک Throne دسکتاپ آمده باشد که API را با کلید خودش روشن کرده است. از آن کلید به‌عنوان `THRONE_KEY` استفاده کنید (در برنامهٔ دسکتاپ: `Integration Settings` → `Throne API` → `Copy for scripts`)، یا Throne را با `--api-key-file` اجرا کنید.

## درخواست‌های مدیریتی {#admin-requests}

درخواست‌هایی که آنچه را Throne در اختیار دارد تغییر می‌دهند، درخواست‌های مدیریتی هستند: `import`، `sub add`، `group`، `profile rm`، `settings set`، `backup`، `restart` و `shutdown`. `logs` هم همین‌طور است، چون گزارش، سایت‌هایی را که اتصال‌ها به آن‌ها رفته‌اند نام می‌برد. Throne فقط از دستگاه خودش به آن‌ها پاسخ می‌دهد، مگر اینکه با `--api-admin-lan` (`THRONE_API_ADMIN_LAN=1`) اجرا شود. از دستگاه دیگر، این درخواست‌ها با `forbidden` شکست می‌خورند.

همهٔ درخواست‌های دیگر، مانند `status`، `connect`، `tun on` یا `test`، از هر دستگاهی که اجازهٔ دسترسی به API را دارد و کلید را دارد کار می‌کنند.

## نمایه‌ها و اشتراک‌ها {#profiles}

```bash
thronectl import links.txt                        # share links, a subscription body, Clash, sing-box, Xray, WireGuard or OpenVPN
thronectl sub add 'https://sub.example.com/abc' --name Provider
thronectl groups
thronectl profiles --group 3
thronectl connect 12                              # or: thronectl connect best --group 3
thronectl tun on
```

- `thronectl import` فایلی تا حجم 900 KiB را می‌خواند، یا با `-` ورودی استاندارد را. `--url URL` باعث می‌شود Throne یک URL را یک بار دانلود کند. بدون `--group`، نمایه‌ها به گروهی می‌روند که در Throne گروه فعلی است.
- `thronectl sub add` یک گروه اشتراک اضافه می‌کند و آن را دانلود می‌کند؛ نام گروه، نام میزبان (host) نشانی URL است، مگر اینکه `--name` بدهید. این گروه به‌طور خودکار به‌روزرسانی می‌شود، مگر اینکه `--no-auto-update` را اضافه کنید.
- `thronectl sub add --route ID` به گروه جدید نمایهٔ مسیریابی خودش را می‌دهد، با شناسه‌ای از `thronectl routes`. `--import-route` نمایهٔ مسیریابی‌ای را که اشتراک فراهم می‌کند نگه می‌دارد و در هر به‌روزرسانی آن را تازه می‌کند. [نمایهٔ مسیریابی برای هر گروه](@/guides/routing.fa.md#group-routing) را ببینید. `thronectl groups` نمایهٔ مسیریابی هر گروه را زیر `ROUTE` نشان می‌دهد.
- `thronectl connect best` نمایه‌ای را که کمترین تأخیر را دارد اجرا می‌کند، و پیش از آن نمایه‌هایی را که نتیجهٔ تست تازه ندارند تست می‌کند (`--max-age`، برای مثال `--max-age 1h`).
- `thronectl test latency --group 3` یک گروه را تست می‌کند. تست‌ها ممکن است چند دقیقه طول بکشند؛ به آن‌ها `--timeout` طولانی‌تری بدهید، برای مثال `thronectl --timeout 5m test latency --group 3`.

نمایهٔ در حال اجرا در هر بار شروع Throne دوباره اجرا می‌شود، و اگر TUN روشن بوده، همراه با TUN، تا وقتی که `thronectl disconnect` را اجرا کنید. برای اینکه یک بار بدون آن شروع کنید، از [`--no-resume`](@/headless/command_line.fa.md#options) استفاده کنید.

## انتقال پیکربندی دسکتاپ {#from-desktop}

برخی چیزها را راحت‌تر می‌توان در یک Throne دسکتاپ تنظیم کرد، مانند نمایه‌های مسیریابی، حساب‌های OTP یا ورود به حساب در VPN. آن‌ها را آنجا تنظیم کنید، سپس با یک پشتیبان منتقلشان کنید:

```bash
# On the desktop: make a backup in the Backup and Restore settings, or, with the desktop's Throne API on:
thronectl backup create -o desktop.thrbackup
scp desktop.thrbackup box:

# On the box:
thronectl backup restore desktop.thrbackup
```

- `backup restore` محتوای آرشیو را نشان می‌دهد و پیش از بازیابی می‌پرسد. بدون ترمینال، `--yes` را اضافه کنید.
- به‌طور پیش‌فرض نمایه‌ها، نمایه‌های مسیریابی، حساب‌های OTP و فهرست‌های IP را بازیابی می‌کند. برای بازیابی تنظیمات هم، `--parts profiles,routes,otp,ip_lists,settings` را اضافه کنید.
- `profiles` و `routes` را با هم بازیابی کنید، همان‌طور که پیش‌فرض این کار را می‌کند. اگر فقط یکی از آن‌ها را بازیابی کنید، گروه‌ها نمایه‌های مسیریابی خودشان را از دست می‌دهند و از نمایهٔ فعال پیروی می‌کنند.
- بازیابی تنظیمات، تنظیماتی را که به این دستگاه تعلق دارند نگه می‌دارد: Throne API، نشانی و پورت‌های ورودی، TUN، کیل‌سوییچ، NTP، گزارش‌گیری و مسیرهای فایل.
- Throne پس از بازیابی دوباره راه‌اندازی می‌شود.
- از طریق API، Throne آرشیوهایی را که نمایه‌هایی دارند که روی دستگاه آن برنامه اجرا می‌کنند یا فایل می‌خوانند، مانند نمایه‌های هستهٔ اضافی (Extra Core)، رد می‌کند (`invalid_archive`).
- آرشیوهای پشتیبان، اطلاعات ورود نمایه‌ها و رمزهای OTP شما را به‌صورت متن ساده نگه می‌دارند. وقتی کارتان تمام شد، آن‌ها را حذف کنید.

برای پشتیبان‌گیری در برنامهٔ دسکتاپ، [پشتیبان‌گیری و بازیابی](@/guides/backup.fa.md#backup-restore) را ببینید.

## تنظیمات {#settings}

`thronectl settings` تنظیماتی را که API می‌تواند تغییر دهد، همراه با مقدار فعلی‌شان فهرست می‌کند. `thronectl settings set` یک یا چند تنظیم را تغییر می‌دهد:

```bash
thronectl settings
thronectl settings get log_level
thronectl settings set kill_switch=true log_level=debug
```

`true`، `false` و اعداد به همان شکل فرستاده می‌شوند و هر چیز دیگری به‌صورت متن. این تنظیمات شامل مواردی است که `Throne --check` و خطای `tun_conflict` نام می‌برند: محدوده‌های نشانی TUN (`tun_ipv4_cidr`، `tun_ipv6_cidr`)، پورت DNS هسته (`dns_in_port`)، ورودی (`inbound_address`، `inbound_port`)، Clash API (`clash_api_port`، `clash_listen_addr`) و sing-box API (`singbox_api_port`). برخی تنظیمات فقط وقتی اعمال می‌شوند که نمایه دوباره اجرا شود؛ [جدول تنظیمات](https://github.com/throneproj/Throne-api/blob/main/spec/api.md#settings) در مشخصات API می‌گوید کدام‌ها.

`thronectl settings set dns_in_lan=true` به دستگاه‌های دیگر شبکه اجازه می‌دهد از سرور DNS هسته استفاده کنند، برای مثال یک روتر؛ [سرور DNS برای دستگاه‌های دیگر](@/guides/dns.fa.md#dns-server) را ببینید.

تنظیماتی را که API مدیریت نمی‌کند فقط می‌توان در یک Throne دسکتاپ تغییر داد. آن‌ها را با یک [بازیابی تنظیمات](#from-desktop) منتقل کنید.

## مدیریت از دستگاه دیگر {#remote}

به‌طور پیش‌فرض API فقط روی `127.0.0.1` گوش می‌دهد. برای مدیریت Throne از دستگاه دیگر، اجازه دهید API با یک فهرست مجاز روی شبکه گوش دهد، و اگر آن دستگاه باید وارد کند و تنظیمات را تغییر دهد، درخواست‌های مدیریتی را مجاز کنید. برای یونیت systemd، این را در `/etc/throne/throne.env` بگذارید و `sudo systemctl restart throne` را اجرا کنید:

```bash
THRONE_API_LISTEN=*:9095
THRONE_API_ALLOW=192.168.1.0/24
THRONE_API_ADMIN_LAN=1
```

در دستگاه دیگر، کلید را در فایلی کپی کنید که فقط خودتان بتوانید آن را بخوانید، `THRONE_KEY_FILE` را روی آن تنظیم کنید و `THRONE_ADDR=<box>:9095` را تنظیم کنید.

- [کیل‌سوییچ](@/advanced/kill_switch.fa.md) همیشه اجازه می‌دهد پاسخ‌های API خارج شوند، بنابراین کلاینت‌ها از هر جایی که وصل شوند به API می‌رسند.
- ترافیک API رمزگذاری و احراز هویت می‌شود، حتی در LAN. با این حال، API را روی loopback نگه دارید مگر اینکه آن را روی شبکه لازم داشته باشید، و همیشه یک فهرست مجاز بدهید.
- پس از 5 احراز هویت ناموفق در یک دقیقه از یک نشانی، Throne آن نشانی را به مدت 60 ثانیه رد می‌کند. یک کلاینت با کلید قدیمی می‌تواند دسترسی کلاینت‌های دیگر پشت همان NAT را قطع کند.
- در Linux، وقتی TUN در حالی شروع می‌شود که API روی یک نشانی شبکه گوش می‌دهد، اگر ممکن باشد کلاینت‌های راه‌دور دسترسی به API را از دست بدهند، Throne یک بار هشدار می‌دهد. [Throne چگونه در دسترس می‌ماند](@/headless/recovery.fa.md#protection) را ببینید.

## فرمان‌ها {#commands}

پرکاربردترین فرمان‌ها:

| فرمان | کاری که انجام می‌دهد |
| --- | --- |
| `thronectl status` | وضعیت، نمایهٔ در حال اجرا، TUN، نمایهٔ مسیریابی، نسخه، توانایی‌ها (capabilities)، و تداخل TUN در صورت وجود |
| `thronectl groups`، `thronectl profiles [--group N]` | گروه‌ها و نمایه‌ها با شناسه‌ها و آخرین نتایج تست‌شان |
| `thronectl connect <id>`، `connect best`، `connect last` | اجرای یک نمایه |
| `thronectl disconnect`، `thronectl toggle` | توقف نمایه؛ اتصال یا قطع اتصال |
| `thronectl tun on`، `tun off` | حالت TUN |
| `thronectl routes`، `thronectl route <id>` | فهرست کردن و فعال کردن نمایه‌های مسیریابی |
| `thronectl test latency \| ip \| speed --group N` | تست نمایه‌ها |
| `thronectl sub update --all` | به‌روزرسانی همهٔ اشتراک‌ها |
| `thronectl geo update`، `thronectl rulesets update` | دانلود دوبارهٔ فایل‌های geo و مجموعه‌قوانین |
| `thronectl traffic --by app --since 7d` | مجموع ترافیک |
| `thronectl logs -n 50`، `thronectl logs -f` | آخرین خطوط گزارش Throne؛ با `-f`، سپس خطوط جدید همان‌طور که Throne آن‌ها را می‌نویسد. [گزارش‌ها](@/headless/monitoring.fa.md#logs) را ببینید |
| `thronectl restart`، `thronectl shutdown` | راه‌اندازی دوبارهٔ Throne؛ خارج کردن آن |

برای خروجی JSON که برای اسکریپت‌ها مناسب است، `--json` را به هر فرمانی اضافه کنید. `thronectl help <command>` یک فرمان را توضیح می‌دهد. برای همهٔ فرمان‌ها و پرچم‌ها و کدهای خروج `thronectl`، [Throne API و thronectl](@/advanced/api.fa.md#commands) را ببینید.

`thronectl restart`، بازیابی پشتیبان و `SIGHUP`، Throne را دوباره راه‌اندازی می‌کنند؛ نحوهٔ آن به [حالت راه‌اندازی دوباره](@/headless/command_line.fa.md#restart-modes) بستگی دارد. `thronectl shutdown` باعث می‌شود Throne با کد 0 خارج شود. زیر systemd، launchd و سرویس Windows، پس از آن خاموش می‌ماند؛ Docker با `restart: unless-stopped` آن را دوباره شروع می‌کند.

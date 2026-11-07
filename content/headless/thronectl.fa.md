+++
title = "مدیریت با thronectl"
description = "thronectl را به Throne بدون رابط گرافیکی متصل کنید، نمایه‌ها و اشتراک‌ها را اضافه کنید، پیکربندی دسکتاپ را منتقل کنید و Throne را از دستگاه دیگری مدیریت کنید."
weight = 20
toc = true
+++

Throne بدون رابط گرافیکی پنجره ندارد. آن را با `thronectl`، کلاینت خط فرمان [Throne API](@/advanced/api.fa.md)، یا از مرورگر با [رابط وب](@/headless/web.fa.md) مدیریت می‌کنید.

## اتصال thronectl به Throne {#connect}

بسته‌های `.deb` و `.rpm`، `thronectl` را با نام `/usr/bin/thronectl` نصب می‌کنند، فایل ZIP مخصوص Linux آن را کنار `Throne` دارد و ایمیج Docker آن را در `/usr/local/bin` دارد. روی macOS، Windows و رایانه‌های دیگری که Throne را از آن‌ها مدیریت می‌کنید، خودتان [thronectl را نصب کنید](@/advanced/api.fa.md#install-thronectl).

`thronectl` نشانی Throne را از `THRONE_ADDR` (پیش‌فرض `127.0.0.1:9095`) می‌خواند و کلید را از فایلی که `THRONE_KEY_FILE` نام می‌برد، از `THRONE_KEY` یا از `--key-file`. هرگز خود کلید را به‌عنوان آرگومان نمی‌پذیرد. `Throne --print-connection` هر دو متغیر را چاپ می‌کند. آن را با کاربری که Throne با آن اجرا می‌شود و با همان `--data-dir` اجرا کنید:

```bash
$ sudo /opt/Throne/Throne --print-connection
export THRONE_ADDR=127.0.0.1:9095
export THRONE_KEY_FILE=/var/lib/throne/config/api.key
```

- **یونیت کاربری systemd:** `eval "$(/opt/Throne/Throne --print-connection)"`، سپس `thronectl status`.
- **Throne با root اجرا می‌شود** (یونیت سیستمی، LaunchDaemon در macOS): فقط root می‌تواند فایل کلید را بخواند و `sudo` متغیرها را حذف می‌کند. کلید را در فایلی متعلق به خودتان کپی کنید:

  ```bash
  mkdir -p ~/.config/throne
  sudo install -m 600 -o "$USER" /var/lib/throne/config/api.key ~/.config/throne/api-key
  export THRONE_KEY_FILE=~/.config/throne/api-key
  thronectl status
  ```

  یا `thronectl` را با root اجرا کنید: `sudo thronectl --key-file /var/lib/throne/config/api.key status`.
- **Docker:** `docker compose exec throne thronectl --key-file /var/lib/throne/config/api.key status`.
- **سرویس Windows:** [اجرای فرمان‌ها از خط فرمان](@/headless/installation.fa.md#windows-prompt) را ببینید.

خط `export` را در فایل راه‌اندازی پوستهٔ خود، مانند `~/.bashrc`، بگذارید. برای تکمیل فرمان‌ها و شناسه‌ها در پوسته، [تکمیل خودکار در پوسته](@/advanced/api.fa.md#completion) را ببینید.

## فرمان‌های روزمره {#profiles}

```bash
thronectl status
thronectl import links.txt               # share links, a subscription, Clash, sing-box, Xray, WireGuard or OpenVPN
thronectl sub add 'https://sub.example.com/abc' --name Provider
thronectl profiles --group 3
thronectl connect 12                     # or: thronectl connect best --group 3
thronectl tun on
thronectl routes                         # then: thronectl route <id>
thronectl logs -f
thronectl disconnect
```

- `import` فایلی تا حجم 900 KiB را می‌خواند، یا با `-` ورودی استاندارد را؛ `--url URL` باعث می‌شود Throne یک URL را یک بار دانلود کند. بدون `--group`، نمایه‌ها به گروهی می‌روند که Throne نشان می‌دهد.
- `sub add --route ID` به گروه جدید [نمایهٔ مسیریابی](@/guides/routing.fa.md#group-routing) خودش را می‌دهد؛ `--import-route` از نمایه‌ای استفاده می‌کند که اشتراک فراهم می‌کند.
- `connect best` نمایه‌ای را که کمترین تأخیر را دارد اجرا می‌کند، پس از تست نمایه‌هایی که نتیجهٔ تازه ندارند (`--max-age 1h`).
- تست‌ها ممکن است چند دقیقه طول بکشند، پس به آن‌ها زمان بیشتری بدهید: `thronectl --timeout 5m test latency --group 3`.
- `thronectl restart`، Throne را طبق [حالت راه‌اندازی دوبارهٔ](@/headless/command_line.fa.md#restart-modes) آن دوباره راه‌اندازی می‌کند. `thronectl shutdown` باعث می‌شود با کد 0 خارج شود: systemd، launchd و سرویس Windows آن را متوقف نگه می‌دارند، اما Docker با `restart: unless-stopped` دوباره شروعش می‌کند.

برای خروجی JSON، `--json` را اضافه کنید؛ `thronectl help <command>` یک فرمان را توضیح می‌دهد. همهٔ فرمان‌ها و کدهای خروج: [فرمان‌ها](@/advanced/api.fa.md#commands).

## انتقال پیکربندی دسکتاپ {#from-desktop}

حساب‌های OTP، ورود به VPN و نمایه‌های مسیریابی را راحت‌تر می‌توان در یک Throne دسکتاپ تنظیم کرد. آن‌ها را آنجا تنظیم کنید، سپس با یک پشتیبان منتقلشان کنید:

```bash
# On the desktop: make a backup in the Backup and Restore settings, or, with the desktop's Throne API on:
thronectl backup create -o desktop.thrbackup
scp desktop.thrbackup box:

# On the box:
thronectl backup restore desktop.thrbackup
```

- `backup restore` محتوای آرشیو را نشان می‌دهد و پیش از بازیابی می‌پرسد. بدون ترمینال، `--yes` را اضافه کنید. Throne پس از بازیابی دوباره راه‌اندازی می‌شود.
- به‌طور پیش‌فرض نمایه‌ها، نمایه‌های مسیریابی، حساب‌های OTP و فهرست‌های IP را بازیابی می‌کند. برای بازیابی تنظیمات هم، `--parts profiles,routes,otp,ip_lists,settings` را اضافه کنید. بازیابی تنظیمات، تنظیماتی را که به این دستگاه تعلق دارند نگه می‌دارد: Throne API، نشانی و پورت‌های ورودی، TUN، کیل‌سوییچ، NTP، گزارش‌گیری و مسیرهای فایل.
- `profiles` و `routes` را با هم بازیابی کنید. اگر یکی به‌تنهایی بازیابی شود، گروه‌ها نمایه‌های مسیریابی خودشان را از دست می‌دهند.
- Throne آرشیوهایی را که نمایه‌هایی دارند که روی دستگاه آن برنامه اجرا می‌کنند یا فایل می‌خوانند، مانند نمایه‌های هستهٔ اضافی (Extra Core)، رد می‌کند (`invalid_archive`).
- پشتیبان‌ها اطلاعات ورود نمایه‌ها و رمزهای OTP شما را به‌صورت متن ساده نگه می‌دارند. وقتی کارتان تمام شد، آن‌ها را حذف کنید.

برای پشتیبان‌گیری در برنامهٔ دسکتاپ، [پشتیبان‌گیری و بازیابی](@/guides/backup.fa.md#backup-restore) را ببینید.

## تنظیمات {#settings}

```bash
thronectl settings
thronectl settings set kill_switch=true log_level=debug
```

`thronectl settings` تنظیماتی را که API مدیریت می‌کند، همراه با مقدار فعلی‌شان فهرست می‌کند؛ [تنظیمات](@/advanced/api.fa.md#settings) آن‌ها را توضیح می‌دهد. بقیه را فقط می‌توان در یک Throne دسکتاپ تغییر داد: آن‌ها را با یک [بازیابی تنظیمات](#from-desktop) منتقل کنید.

## مدیریت از دستگاه دیگر {#remote}

API فقط روی `127.0.0.1` گوش می‌دهد. برای مدیریت Throne از دستگاه دیگر، اجازه دهید با یک فهرست مجاز روی شبکه گوش دهد. برای یونیت systemd، این را در `/etc/throne/throne.env` بگذارید و `sudo systemctl restart throne` را اجرا کنید:

```bash
THRONE_API_LISTEN=*:9095
THRONE_API_ALLOW=192.168.1.0/24
THRONE_API_ADMIN_LAN=1
```

در دستگاه دیگر، کلید را در فایلی بگذارید که فقط خودتان بتوانید آن را بخوانید، و `THRONE_KEY_FILE` را روی آن فایل و `THRONE_ADDR` را روی `<box>:9095` تنظیم کنید.

- **درخواست‌های مدیریتی**، یعنی وارد کردن، افزودن اشتراک، حذف، تغییر تنظیمات، پشتیبان‌گیری، خواندن گزارش، راه‌اندازی دوباره و خاموش کردن، فقط از دستگاه خود Throne پاسخ داده می‌شوند، مگر اینکه `THRONE_API_ADMIN_LAN=1` (`--api-admin-lan`) تنظیم شده باشد. در غیر این صورت با `forbidden` شکست می‌خورند. اگر دستگاه‌های دیگر فقط باید وضعیت را ببینند و نمایه عوض کنند، آن را حذف کنید.
- پس از 5 احراز هویت ناموفق در یک دقیقه از یک نشانی، Throne آن نشانی را به مدت 60 ثانیه رد می‌کند. یک کلاینت با کلید قدیمی می‌تواند دسترسی کلاینت‌های دیگر پشت همان NAT را قطع کند.
- [کیل‌سوییچ](@/advanced/kill_switch.fa.md) همیشه اجازه می‌دهد پاسخ‌های API خارج شوند. در Linux، TUN ممکن است پاسخ‌ها به کلاینت‌های راه‌دور را بگیرد؛ [Throne چگونه در دسترس می‌ماند](@/headless/recovery.fa.md#protection) را ببینید.
- ترافیک API رمزگذاری و احراز هویت می‌شود. با این حال، API را روی loopback نگه دارید مگر اینکه آن را روی شبکه لازم داشته باشید، و همیشه یک فهرست مجاز تنظیم کنید.

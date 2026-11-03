+++
title = "Throne بدون رابط گرافیکی (Headless)"
description = "Throne را بدون پنجره روی سرور، روتر، Raspberry Pi یا در کانتینر اجرا کنید و آن را با thronectl کنترل کنید."
weight = 4
sort_by = "weight"
toc = true
+++

`Throne --headless` برنامهٔ Throne را بدون پنجره اجرا می‌کند: روی سرور، روتر، Raspberry Pi، NAS یا در کانتینر. این همان برنامهٔ دسکتاپ است، با همان هسته، نمایه‌ها، نمایه‌های مسیریابی و تنظیمات، اما هیچ پنجره، آیکون سینی سیستم یا کادر گفت‌وگویی نشان نمی‌دهد. آن را از ترمینال با `thronectl` و از طریق [Throne API](@/advanced/api.fa.md) کنترل می‌کنید؛ Throne بدون رابط گرافیکی این API را در نخستین اجرا روشن می‌کند.

Throne بدون رابط گرافیکی از Throne 1.4.0 در Linux، macOS و Windows در دسترس است. Throne برای Android حالت بدون رابط گرافیکی (headless) ندارد.

کاربردهای رایج:

- **دستگاه پروکسی.** Throne یک نمایه را اجرا می‌کند و دستگاه‌های دیگر شبکه از ورودی mixed آن (HTTP و SOCKS) استفاده می‌کنند. [اشتراک‌گذاری پروکسی با دستگاه‌های دیگر](@/guides/proxy_modes.fa.md#lan-sharing) را ببینید.
- **دروازهٔ شبکه (gateway).** Throne در حالت TUN روی دستگاهی اجرا می‌شود که ترافیک شبکه را مسیریابی می‌کند.
- **کانتینر.** ایمیج Docker برنامهٔ Throne را اجرا می‌کند و داده‌های آن را در یک volume نگه می‌دارد.
- **رایانهٔ خودتان، بدون پنجره.** یک یونیت کاربری systemd نمایه‌های دسکتاپ شما را پس از خروج از حساب کاربری هم متصل نگه می‌دارد.

صفحه‌های این بخش:

- [نصب و سرویس‌ها](@/headless/installation.fa.md): بسته‌های Linux، Docker، macOS و سرویس Windows.
- [مدیریت با thronectl](@/headless/thronectl.fa.md): اتصال `thronectl`، افزودن اشتراک‌ها، انتقال پیکربندی دسکتاپ، تغییر تنظیمات.
- [رابط وب](@/headless/web.fa.md): مدیریت Throne از مرورگر با `thronectl web`.
- [خط فرمان و پوشهٔ داده‌ها](@/headless/command_line.fa.md): همهٔ گزینه‌ها و متغیرها، محل نگهداری داده‌ها، راه‌اندازی‌های دوباره و کدهای خروج.
- [بررسی سلامت و گزارش‌ها](@/headless/monitoring.fa.md): `Throne --check`، `Throne --health`، وضعیت systemd و گزارش‌ها.
- [محافظت در برابر قطع دسترسی و بازیابی](@/headless/recovery.fa.md): اینکه Throne چگونه API را در دسترس نگه می‌دارد، و وقتی تنظیمات دسترسی شما را قطع می‌کنند چه باید کرد.
- [عیب‌یابی](@/headless/troubleshooting.fa.md): مشکلات رایج و راه‌حل‌های آن‌ها.

## شروع سریع {#quick-start}

در Debian 10، Ubuntu 20.04 یا جدیدتر (amd64):

1. فایل `Throne-<version>-debian-amd64.deb` را از [صفحهٔ انتشارها](https://github.com/throneproj/Throne/releases) دانلود کنید. در Debian 12، Ubuntu 22.04 یا جدیدتر، می‌توانید به‌جای آن [مخزن APT](@/get_started/installation.fa.md#apt-repository) را اضافه کنید.
2. بسته را نصب کنید و سرویس را شروع کنید:

   ```bash
   sudo apt install ./Throne-<version>-debian-amd64.deb
   sudo systemctl enable --now throne
   systemctl status throne
   ```

   `systemctl status` نشان می‌دهد Throne در حال انجام چه کاری است، برای مثال `Status: "Idle"`.

3. Throne داده‌هایش را در `/var/lib/throne` و کلید API خود را در `/var/lib/throne/config/api.key` نگه می‌دارد که فقط root می‌تواند آن را بخواند. کلید را در فایلی متعلق به خودتان کپی کنید:

   ```bash
   mkdir -p ~/.config/throne
   sudo install -m 600 -o "$USER" /var/lib/throne/config/api.key ~/.config/throne/api-key
   export THRONE_KEY_FILE=~/.config/throne/api-key
   ```

4. بسته، `thronectl`، کلاینت خط فرمان، را هم با نام `/usr/bin/thronectl` نصب می‌کند. برای مدیریت Throne از رایانهٔ دیگر، [نصب thronectl](@/headless/thronectl.fa.md#install-thronectl) را ببینید.
5. اشتراک خود را اضافه کنید و متصل شوید:

   ```bash
   thronectl status
   thronectl sub add 'https://sub.example.com/abc' --name Provider
   thronectl profiles
   thronectl connect 12
   ```

   به‌جای `12`، شناسهٔ (ID) یکی از نمایه‌های فهرست `thronectl profiles` را بگذارید، یا از `thronectl connect best` استفاده کنید تا نمایه‌ای که کمترین تأخیر را دارد انتخاب شود.

اکنون Throne نمایه را اجرا می‌کند و پس از هر بار راه‌اندازی دوبارهٔ سیستم آن را دوباره اجرا می‌کند، تا وقتی که `thronectl disconnect` را اجرا کنید. برای اینکه پیش از شروع Throne بررسی کنید که آیا این دستگاه اصلاً می‌تواند Throne را اجرا کند، `sudo /opt/Throne/Throne --check` را اجرا کنید. [Throne --check](@/headless/monitoring.fa.md#check) را ببینید.

## Throne بدون رابط گرافیکی چگونه کار می‌کند {#how-it-works}

Throne هرگز خودش را به پس‌زمینه نمی‌فرستد. یک مدیر سرویس آن را هنگام بوت شروع می‌کند، پس از خرابی دوباره شروعش می‌کند و آن را متوقف می‌کند: systemd در Linux، launchd در macOS، Service Control Manager در Windows، یا Docker. مخزن Throne برای هر کدام یک فایل آماده دارد:

| فایل | چیست |
| --- | --- |
| [`dist/linux/throne.service`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne.service) | یونیت سیستمی systemd که با root اجرا می‌شود. بسته‌های `.deb` و `.rpm` آن را به‌صورت `/usr/lib/systemd/system/throne.service` نصب می‌کنند. |
| [`dist/linux/throne.env`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne.env) | فایل محیطی (environment file) این یونیت که به‌صورت `/etc/throne/throne.env` نصب می‌شود. هر متغیر در آن یک نمونهٔ کامنت‌شده است. |
| [`dist/linux/caps.conf`](https://github.com/throneproj/Throne/blob/dev/dist/linux/caps.conf) | فایل drop-in اختیاری: یونیت را به‌جای root با کاربر `throne` و با capabilityها اجرا می‌کند. |
| [`dist/linux/throne-user.service`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne-user.service) | یونیت کاربری systemd، بدون root. |
| [`dist/linux/throne-web.service`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne-web.service)، [`throne-web.env`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne-web.env) | یونیت systemd برای [رابط وب](@/headless/web.fa.md#systemd) که `thronectl web` را با کاربری مخصوص خودش اجرا می‌کند، و فایل محیطی آن. بسته‌ها آن‌ها را به‌صورت `/usr/lib/systemd/system/throne-web.service` و `/etc/throne/web.env` نصب می‌کنند. |
| [`dist/macos/moe.Throne.headless.plist`](https://github.com/throneproj/Throne/blob/dev/dist/macos/moe.Throne.headless.plist) | LaunchDaemon برای launchd. |
| [`dist/windows/install-service.bat`](https://github.com/throneproj/Throne/blob/dev/dist/windows/install-service.bat)، [`uninstall-service.bat`](https://github.com/throneproj/Throne/blob/dev/dist/windows/uninstall-service.bat) | نصب و حذف سرویس Windows. |
| [`docker/compose.yaml`](https://github.com/throneproj/Throne/blob/dev/docker/compose.yaml) | نمونهٔ Docker Compose برای ایمیج `ghcr.io/throneproj/throne`. |

وقتی Throne بدون رابط گرافیکی اجرا می‌شود، این اتفاق‌ها می‌افتد:

- نمایه‌ها، گروه‌ها، نمایه‌های مسیریابی و تنظیماتش را در یک [پوشهٔ داده‌ها](@/headless/command_line.fa.md#data-dir) نگه می‌دارد، در همان پایگاه‌دادهٔ `throne.db` که برنامهٔ دسکتاپ استفاده می‌کند.
- Throne API از نخستین اجرا روی `127.0.0.1:9095` گوش می‌دهد، با کلیدی که Throne آن را در `config/api.key` درون پوشهٔ داده‌ها تولید می‌کند. `thronectl` از همین کلید استفاده می‌کند. [اتصال thronectl به Throne](@/headless/thronectl.fa.md#connect) را ببینید.
- نمایهٔ در حال اجرا و روشن بودن یا نبودن TUN را به خاطر می‌سپارد و در هر بار شروع، آن‌ها را دوباره اجرا می‌کند تا وقتی که اتصال را قطع کنید. `--no-resume` این کار را برای یک بار اجرا خاموش می‌کند.
- وضعیت خود را به مدیر سرویس اعلام می‌کند و پیشرفت کار خودش را زیر نظر دارد. وقتی هسته پشت سر هم شکست می‌خورد، یک نمایه گیر می‌کند یا حلقهٔ اصلی متوقف می‌شود، با کدی خارج می‌شود که به مدیر سرویس می‌گوید آن را دوباره شروع کند. [کدهای خروج](@/headless/command_line.fa.md#exit-codes) را ببینید.
- `Throne --health` از Throne در حال اجرا می‌پرسد که در چه وضعیتی است، و `Throne --check` به شما می‌گوید که آیا رایانه می‌تواند آن را اجرا کند. [بررسی سلامت و گزارش‌ها](@/headless/monitoring.fa.md) را ببینید.

## تفاوت‌ها با برنامهٔ دسکتاپ {#differences}

Throne بدون رابط گرافیکی هیچ چیزی نمی‌پرسد. آنچه را برنامهٔ دسکتاپ در یک کادر گفت‌وگو می‌پرسد، Throne بدون رابط گرافیکی خودش تصمیم می‌گیرد یا در گزارش می‌نویسد:

| وضعیت | Throne بدون رابط گرافیکی |
| --- | --- |
| یک اطلاعیه یا یک خطا | آن را در گزارش می‌نویسد. |
| TUN به دسترسی‌های سیستمی نیاز دارد | با `needs_privileges` رد می‌کند. `Throne --check` می‌گوید چه چیزی کم است. |
| یک نمایهٔ Xray به فایل‌های geoip یا geosite نیاز دارد | آن‌ها را دانلود می‌کند و یک بار دیگر اجرا را امتحان می‌کند. |
| [کیل‌سوییچ](@/advanced/kill_switch.fa.md) نمی‌تواند کار کند | دلیل را در گزارش می‌نویسد، `kill_switch_failed` را گزارش می‌کند و از اجرای نمایه‌ها خودداری می‌کند. |
| یک نمایهٔ VPN کد یک‌بارمصرف می‌خواهد | اگر نمایه [حساب OTP](@/advanced/vpn_profiles.fa.md#otp-manager) داشته باشد، با آن پاسخ می‌دهد. |
| یک نمایهٔ VPN چیز دیگری می‌خواهد، مانند ورود به حساب از طریق وب | وقتی مهلت پرسش تمام شود، حداکثر پس از 5 دقیقه، آن را لغو می‌کند (`vpn_challenge_pending`). اگر سرور اطلاعات ورود ذخیره‌شده را رد کند، نتیجه `vpn_auth_failed` است. |
| یک تنظیم به راه‌اندازی دوبارهٔ برنامه نیاز دارد | آن را در گزارش می‌نویسد. تنظیم پس از راه‌اندازی دوبارهٔ بعدی اعمال می‌شود (`thronectl restart`). |
| شرایط استفادهٔ WARP | هرگز آن‌ها را نمی‌پذیرد. |

این ویژگی‌ها به پنجره نیاز دارند و در Throne بدون رابط گرافیکی در دسترس نیستند:

- پروکسی سیستمی (`thronectl proxy on` با `system_proxy_unsupported` شکست می‌خورد)، آیکون سینی سیستم، کلیدهای میانبر سراسری و ویرایشگرها.
- به‌روزرسان داخلی، و راه‌اندازی دوباره با دسترسی مدیر یا با TUN. از طریق بسته، ایمیج Docker یا نصب‌کننده به‌روزرسانی کنید.
- [لینک‌های عمیق](@/advanced/deeplinks.fa.md) `throne://` و پیوند فایل‌ها با برنامه (file associations). به‌جای آن‌ها با `thronectl import` وارد کنید.
- درخواست‌های ورود به حساب در نمایه‌های VPN، مانند SSO، به‌جز کدهای یک‌بارمصرف از یک حساب OTP.
- ثبت‌نام WARP.
- تنظیماتی که API آن‌ها را مدیریت نمی‌کند، و بازیابی نمایه‌های هستهٔ اضافی (Extra Core) از طریق API.

این موارد را در یک Throne دسکتاپ تنظیم کنید و با یک [پشتیبان](@/headless/thronectl.fa.md#from-desktop) منتقل کنید. برای یک یونیت کاربری systemd، یونیت را متوقف کنید و برنامهٔ دسکتاپ را باز کنید: هر دو از داده‌های یکسانی استفاده می‌کنند.

## اجرا در کنار یک Throne دسکتاپ {#beside-desktop}

رابط TUN (`throne-tun`)، پورت DNS هسته (`dns_in_port`، به‌طور پیش‌فرض 5533) و مسیرهای TUN متعلق به کل رایانه هستند، بنابراین در هر زمان فقط یک Throne می‌تواند روی آن TUN را اجرا کند. Throne بدون رابط گرافیکی فقط وقتی TUN را روشن می‌کند که:

- هیچ رابط `throne-tun` دیگری وجود نداشته باشد (در Linux و Windows بررسی می‌شود)،
- هیچ رابط شبکه‌ای از قبل نشانی TUN را نداشته باشد (`tun_ipv4_cidr`، به‌طور پیش‌فرض `172.19.0.1/24`)، و
- پورت DNS تا وقتی هیچ نمایه‌ای در حال اجرا نیست آزاد باشد.

در غیر این صورت `thronectl tun on` با `tun_conflict` شکست می‌خورد و `thronectl status` نشان می‌دهد چه چیزی TUN را در اختیار دارد. وقتی TUN هنگام شروع نتواند دوباره روشن شود، `Throne --health` وضعیت `tun_unavailable` را گزارش می‌کند. اگر یک bridge در Docker یا شبکهٔ دیگری از محدودهٔ TUN برنامهٔ Throne استفاده می‌کند، TUN را به یک محدودهٔ آزاد ببرید، سپس دوباره روشنش کنید:

```bash
thronectl settings set tun_ipv4_cidr=172.29.0.1/24
thronectl tun on
```

وقتی برنامهٔ دیگری پورت DNS را در اختیار دارد، با `thronectl settings set dns_in_port=PORT` یک پورت آزاد به Throne بدهید.

Throne بدون رابط گرافیکی همچنین اجرای خود را علامت‌گذاری می‌کند: با root در `/run/throne` (در macOS، `/var/run/throne`)، برای یک کاربر در `$XDG_RUNTIME_DIR/throne`، و در Windows به‌صورت `Global\ThroneHeadless` (وقتی نه با دسترسی مدیر اجرا شود و نه به‌صورت سرویس، `Local\ThroneHeadless` که فقط در همان نشست Windows دیده می‌شود). در این صورت یک Throne دسکتاپ روی همان رایانه:

- هنگام شروع هشدار می‌دهد که یک Throne بدون رابط گرافیکی در حال اجراست،
- تا وقتی آن Throne بدون رابط گرافیکی اجرا می‌شود، TUN را رد می‌کند، و
- تا وقتی آن Throne بدون رابط گرافیکی از همان نصب اجرا می‌شود، خودش را به‌روزرسانی نمی‌کند.

دو Throne هرگز از یک پوشهٔ داده‌ها به‌طور مشترک استفاده نمی‌کنند. اگر یک یونیت کاربری بدون رابط گرافیکی از پوشهٔ داده‌های برنامهٔ دسکتاپ استفاده کند، برنامهٔ دسکتاپ می‌گوید که Throne در پس‌زمینه در حال اجراست و خارج می‌شود. ابتدا یونیت را با `systemctl --user stop throne` متوقف کنید. یک Throne بدون رابط گرافیکی دوم با پوشهٔ داده‌های خودش می‌تواند در کنار اولی اجرا شود؛ بررسی‌های بالا مانع می‌شوند که این دو هم‌زمان TUN را اجرا کنند.

## شناسهٔ دستگاه {#device-id}

برخی اشتراک‌ها شناسهٔ دستگاه می‌خواهند (هدر `x-hwid`؛ [User-Agent و HWID](@/guides/subscriptions.fa.md#user-agent-and-hwid) را ببینید). در Linux، Throne آن را از `/etc/machine-id` و در نبود آن از `/var/lib/dbus/machine-id` می‌گیرد. سیستمی که هیچ‌کدام از این دو فایل را ندارد، مانند یک کانتینر، در نخستین اجرا یک شناسهٔ تولیدشده در `config/hwid` می‌گیرد. این شناسه همراه پوشهٔ داده‌ها می‌ماند.

ایمیج رسمی Docker بدون machine ID عرضه می‌شود، بنابراین هر کانتینر شناسهٔ خودش را می‌گیرد. اگر ایمیج خودتان را می‌سازید، فایل `machine-id` را در آن قرار ندهید: در این صورت همهٔ کانتینرهایی که از آن ایمیج ساخته می‌شوند همان شناسه را می‌فرستند.

macOS از UUID سخت‌افزار استفاده می‌کند و Windows از `MachineGuid` در رجیستری.

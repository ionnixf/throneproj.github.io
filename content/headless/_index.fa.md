+++
title = "Throne بدون رابط گرافیکی (Headless)"
description = "Throne را بدون پنجره روی سرور، روتر، Raspberry Pi یا در کانتینر اجرا کنید و آن را با thronectl کنترل کنید."
weight = 4
sort_by = "weight"
toc = true
+++

`Throne --headless` برنامهٔ Throne را بدون پنجره اجرا می‌کند: روی سرور، روتر، Raspberry Pi یا در کانتینر. این همان برنامهٔ دسکتاپ است، با همان هسته، نمایه‌ها و تنظیمات. آن را با `thronectl` و از طریق [Throne API](@/advanced/api.fa.md) کنترل می‌کنید که از نخستین اجرا روشن است. به Throne 1.4.0 یا جدیدتر روی Linux، macOS یا Windows نیاز دارد. Throne برای Android حالت بدون رابط گرافیکی (headless) ندارد.

کاربردهای رایج: دستگاه پروکسی‌ای که [دستگاه‌های دیگر از ورودی mixed آن استفاده می‌کنند](@/guides/proxy_modes.fa.md#lan-sharing)، دروازهٔ TUN برای شبکه، کانتینر Docker، یا یک [یونیت کاربری systemd](@/headless/installation.fa.md#user-unit) که نمایه‌های دسکتاپ شما را پس از خروج از حساب کاربری هم متصل نگه می‌دارد.

## شروع سریع {#quick-start}

در Debian 10، Ubuntu 20.04 یا جدیدتر (amd64):

1. فایل `Throne-<version>-debian-amd64.deb` را از [صفحهٔ انتشارها](https://github.com/throneproj/Throne/releases) دانلود کنید، یا [مخزن APT](@/get_started/installation.fa.md#apt-repository) را اضافه کنید (Debian 12، Ubuntu 22.04 یا جدیدتر).
2. بسته را نصب کنید و سرویس را شروع کنید:

   ```bash
   sudo apt install ./Throne-<version>-debian-amd64.deb
   sudo systemctl enable --now throne
   ```

3. کلید API را که فقط root می‌تواند بخواند، در فایلی متعلق به خودتان کپی کنید:

   ```bash
   mkdir -p ~/.config/throne
   sudo install -m 600 -o "$USER" /var/lib/throne/config/api.key ~/.config/throne/api-key
   export THRONE_KEY_FILE=~/.config/throne/api-key
   ```

4. اشتراک خود را اضافه کنید و با `thronectl` که بسته آن را نصب می‌کند، متصل شوید:

   ```bash
   thronectl status
   thronectl sub add 'https://sub.example.com/abc' --name Provider
   thronectl profiles
   thronectl connect 12
   ```

   به‌جای `12`، شناسهٔ (ID) یکی از نمایه‌های فهرست را بگذارید، یا از `thronectl connect best` استفاده کنید تا نمایه‌ای که کمترین تأخیر را دارد انتخاب شود.

Throne این نمایه را پس از هر بار راه‌اندازی دوبارهٔ سیستم دوباره اجرا می‌کند، تا وقتی که `thronectl disconnect` را اجرا کنید. اگر چیزی کار نمی‌کند، `sudo /opt/Throne/Throne --check` را اجرا کنید ([Throne --check](@/headless/monitoring.fa.md#check) را ببینید). برای Docker، macOS، Windows و نصب‌های دیگر Linux، [نصب و سرویس‌ها](@/headless/installation.fa.md) را ببینید.

## Throne بدون رابط گرافیکی چگونه کار می‌کند {#how-it-works}

- یک مدیر سرویس آن را شروع، دوباره راه‌اندازی و متوقف می‌کند: systemd، launchd، Service Control Manager در Windows یا Docker. Throne هرگز خودش را به پس‌زمینه نمی‌فرستد.
- نمایه‌ها، گروه‌ها، نمایه‌های مسیریابی و تنظیماتش را در یک [پوشهٔ داده‌ها](@/headless/command_line.fa.md#data-dir) نگه می‌دارد، در همان `throne.db` که برنامهٔ دسکتاپ استفاده می‌کند.
- API روی `127.0.0.1:9095` گوش می‌دهد، با کلیدی که Throne آن را در `config/api.key` درون پوشهٔ داده‌ها می‌سازد. [اتصال thronectl به Throne](@/headless/thronectl.fa.md#connect) را ببینید.
- در هر بار شروع، آخرین نمایه را دوباره اجرا می‌کند، و اگر TUN روشن بوده، همراه با TUN. `--no-resume` این کار را یک بار کنار می‌گذارد.
- وقتی هسته پشت سر هم شکست می‌خورد یا یک نمایه گیر می‌کند، با کدی خارج می‌شود که مدیر سرویس را وادار می‌کند آن را دوباره شروع کند. [کدهای خروج](@/headless/command_line.fa.md#exit-codes) را ببینید.

## تفاوت‌ها با برنامهٔ دسکتاپ {#differences}

Throne بدون رابط گرافیکی هیچ چیزی نمی‌پرسد. خودش تصمیم می‌گیرد یا در گزارش می‌نویسد:

- روشن کردن TUN بدون دسترسی‌های لازم با `needs_privileges` شکست می‌خورد. `Throne --check` می‌گوید چه چیزی کم است.
- اگر [کیل‌سوییچ](@/advanced/kill_switch.fa.md) نتواند کار کند، Throne وضعیت `kill_switch_failed` را گزارش می‌کند و از اجرای نمایه‌ها خودداری می‌کند.
- نمایهٔ VPN که کد یک‌بارمصرف می‌خواهد، آن را از [حساب OTP](@/advanced/vpn_profiles.fa.md#otp-manager) خودش می‌گیرد. هر پرسش دیگری، مانند ورود به حساب از طریق وب، وقتی مهلتش تمام شود لغو می‌شود، حداکثر پس از 5 دقیقه (`vpn_challenge_pending`). اگر سرور اطلاعات ورود ذخیره‌شده را رد کند، نتیجه `vpn_auth_failed` است.
- تنظیمی که به راه‌اندازی دوبارهٔ برنامه نیاز دارد، پس از `thronectl restart` اعمال می‌شود.

پروکسی سیستمی (`thronectl proxy on` با `system_proxy_unsupported` شکست می‌خورد)، آیکون سینی سیستم، کلیدهای میانبر سراسری و به‌روزرسان داخلی وجود ندارند: از طریق بسته، ایمیج Docker یا نصب‌کننده به‌روزرسانی کنید. [لینک‌های عمیق](@/advanced/deeplinks.fa.md) `throne://` و پیوند فایل‌ها با برنامه (file associations) کار نمی‌کنند؛ به‌جای آن‌ها با `thronectl import` وارد کنید.

حساب‌های OTP، اطلاعات ورود VPN و تنظیماتی را که API مدیریت نمی‌کند، در یک Throne دسکتاپ تنظیم کنید و با یک [پشتیبان](@/headless/thronectl.fa.md#from-desktop) منتقل کنید. یک یونیت کاربری systemd داده‌هایش را با برنامهٔ دسکتاپ مشترک دارد: یونیت را متوقف کنید و برنامه را باز کنید.

## اجرا در کنار یک Throne دسکتاپ {#beside-desktop}

در هر زمان فقط یک Throne می‌تواند روی یک رایانه TUN را اجرا کند، چون رابط `throne-tun`، محدودهٔ نشانی TUN (`tun_ipv4_cidr`، به‌طور پیش‌فرض `172.19.0.1/24`) و پورت DNS هسته (`dns_in_port`، به‌طور پیش‌فرض 5533) متعلق به کل رایانه هستند. وقتی یکی از آن‌ها در اختیار چیز دیگری باشد، `thronectl tun on` با `tun_conflict` شکست می‌خورد و `thronectl status` نشان می‌دهد چه چیزی آن را در اختیار دارد. اگر یک bridge در Docker یا شبکهٔ دیگری از محدودهٔ TUN استفاده می‌کند، TUN را به یک محدودهٔ آزاد ببرید؛ اگر برنامهٔ دیگری پورت DNS را در اختیار دارد، پورت دیگری به Throne بدهید:

```bash
thronectl settings set tun_ipv4_cidr=172.29.0.1/24    # or: dns_in_port=PORT
thronectl tun on
```

تا وقتی یک Throne بدون رابط گرافیکی اجرا می‌شود، یک Throne دسکتاپ روی همان رایانه هنگام شروع هشدار می‌دهد، TUN را رد می‌کند، و اگر هر دو از همان نصب اجرا شوند، خودش را به‌روزرسانی نمی‌کند.

دو Throne هرگز از یک پوشهٔ داده‌ها به‌طور مشترک استفاده نمی‌کنند. اگر یک یونیت کاربری از پوشهٔ داده‌های برنامهٔ دسکتاپ استفاده کند، برنامهٔ دسکتاپ می‌گوید که Throne در پس‌زمینه در حال اجراست و خارج می‌شود. ابتدا یونیت را متوقف کنید: `systemctl --user stop throne`.

## شناسهٔ دستگاه {#device-id}

برخی اشتراک‌ها شناسهٔ دستگاه می‌خواهند ([User-Agent و HWID](@/guides/subscriptions.fa.md#user-agent-and-hwid) را ببینید). در Linux، Throne آن را از `/etc/machine-id` و در نبود آن از `/var/lib/dbus/machine-id` می‌خواند. اگر هیچ‌کدام نباشد، مانند یک کانتینر، در نخستین اجرا یک شناسه در `config/hwid` می‌سازد که همراه پوشهٔ داده‌ها می‌ماند. macOS از UUID سخت‌افزار استفاده می‌کند و Windows از `MachineGuid` در رجیستری.

ایمیج رسمی Docker بدون machine ID عرضه می‌شود، بنابراین هر کانتینر شناسهٔ خودش را می‌گیرد. اگر ایمیج خودتان را می‌سازید، فایل `machine-id` را در آن قرار ندهید، وگرنه همهٔ کانتینرهایی که از آن ساخته می‌شوند همان شناسه را می‌فرستند.

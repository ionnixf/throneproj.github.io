+++
title = "نصب و سرویس‌ها"
description = "Throne بدون رابط گرافیکی را به‌صورت سرویس systemd، یونیت کاربری systemd، کانتینر Docker، LaunchDaemon در macOS یا سرویس Windows نصب کنید."
weight = 10
toc = true
+++

Throne بدون رابط گرافیکی همان برنامهٔ معمولی Throne است که با `--headless` اجرا می‌شود. Throne را همان‌طور که در [نصب](@/get_started/installation.fa.md) آمده است نصب کنید، سپس اجرای آن را به یک مدیر سرویس بسپارید. این صفحه روش کار را برای هر سیستم نشان می‌دهد:

| سیستم | مدیر سرویس | با چه کاربری اجرا می‌شود | پوشهٔ داده‌ها |
| --- | --- | --- | --- |
| Debian، Ubuntu، Raspberry Pi OS، Fedora، openSUSE | [یونیت سیستمی systemd](#linux-packages) | root (یا کاربر `throne`) | `/var/lib/throne` |
| هر توزیع Linux، بدون root | [یونیت کاربری systemd](#user-unit) | خودتان | `~/.config/Throne` |
| Docker | [Docker یا Docker Compose](#docker) | root درون کانتینر | volume با مسیر `/var/lib/throne` |
| macOS | [LaunchDaemon در launchd](#macos) | root | `/Library/Application Support/Throne` |
| Windows | [سرویس Windows](#windows) | LocalSystem | `C:\ProgramData\Throne` |

پس از نصب، `thronectl` را همان‌طور که در [مدیریت با thronectl](@/headless/thronectl.fa.md) آمده است متصل کنید.

## Debian، Ubuntu، Raspberry Pi OS، Fedora، openSUSE {#linux-packages}

بسته‌های `.deb` و `.rpm` از [صفحهٔ انتشارها](https://github.com/throneproj/Throne/releases) و از مخازن بسته، Throne را در `/opt/Throne` نصب می‌کنند و یونیت systemd با نام `throne.service` و فایل محیطی `/etc/throne/throne.env` را هم نصب می‌کنند. بسته را بر اساس کتابخانهٔ C سیستم (glibc) انتخاب کنید:

| بسته | نیاز دارد به | برای مثال روی این سیستم‌ها اجرا می‌شود |
| --- | --- | --- |
| `Throne-<version>-debian-amd64.deb`، `…-fedora-amd64.rpm` | glibc 2.28 | Debian 10، Ubuntu 20.04، RHEL 8 و جدیدتر، Fedora، openSUSE |
| `…-amd64-system-qt.deb`، `…-amd64-system-qt.rpm` | glibc 2.34 و Qt 6 سیستم | Debian 12، Ubuntu 22.04 و جدیدتر، Fedora، openSUSE Tumbleweed، با Qt توزیع |
| `Throne-<version>-debian-arm64.deb`، `…-fedora-arm64.rpm` | glibc 2.28 | Raspberry Pi OS bullseye، Debian 10، Ubuntu 20.04 و جدیدتر روی arm64 |
| `Throne-<version>-debian-arm64-system-qt.deb` | glibc 2.34 و Qt 6 سیستم | Raspberry Pi OS bookworm، Debian 12، Ubuntu 22.04 و جدیدتر روی arm64 |

هر بسته به نسخه‌ای از glibc وابسته است که فایل‌هایش لازم دارند، بنابراین مدیر بسته، بسته‌ای را که برای سیستم بیش از حد جدید است رد می‌کند، به‌جای اینکه Throne‌ای نصب کند که نمی‌تواند اجرا شود. روی Raspberry Pi از نسخهٔ 64 بیتی Raspberry Pi OS استفاده کنید. روی bookworm و جدیدتر، هر دو بستهٔ arm64 کار می‌کنند. bullseye، Qt 6 ندارد؛ پس آنجا از بستهٔ معمولی arm64 استفاده کنید.

بسته‌های بدون `system-qt` نسخهٔ Qt خودشان را همراه دارند، اما همچنان به کتابخانه‌های گرافیک، فونت و X11 سیستم وابسته‌اند، چون یک برنامهٔ واحد Throne برنامهٔ دسکتاپ را هم در خود دارد. مدیر بسته این کتابخانه‌ها را نصب می‌کند. به هیچ نمایشگری نیاز نیست.

بسته را نصب کنید و سرویس را شروع کنید:

```bash
sudo apt install ./Throne-<version>-debian-amd64.deb   # or: sudo dnf install ./Throne-<version>-fedora-amd64.rpm
sudo /opt/Throne/Throne --check                        # optional: can this computer run it?
sudo systemctl enable --now throne
```

- بسته هرگز خودش یونیت را فعال یا شروع نمی‌کند. ارتقای بسته، اگر یونیت در حال اجرا باشد، آن را دوباره راه‌اندازی می‌کند و حذف بسته آن را متوقف و غیرفعال می‌کند.
- `/var/lib/throne` (نمایه‌ها، تنظیمات و کلید API) پس از حذف بسته، حتی با `apt purge`، باقی می‌ماند. برای شروع از نو، خودتان آن را حذف کنید.
- `systemctl start throne` وقتی برمی‌گردد که Throne شروع به کار کرده باشد. [systemd: آمادگی، وضعیت و watchdog](@/headless/monitoring.fa.md#systemd) را ببینید.
- `/etc/throne/throne.env` برای هر متغیری که یونیت می‌تواند استفاده کند یک نمونهٔ کامنت‌شده دارد. کامنت یک خط را بردارید و `sudo systemctl restart throne` را اجرا کنید. ارتقاها تغییرات شما را نگه می‌دارند. هرگز کلید API را در این فایل نگذارید؛ به‌جای آن، `THRONE_API_KEY_FILE` نام یک فایل کلید را مشخص می‌کند.
- گزارش‌ها: `journalctl -u throne -f`.
- بسته‌ها `thronectl` را هم با نام `/usr/bin/thronectl` نصب می‌کنند، و یک یونیت دوم به نام `throne-web.service` برای [رابط وب](@/headless/web.fa.md#systemd). آن هم فعال نمی‌شود.

برای تغییر خط فرمان، با `sudo systemctl edit throne` یک drop-in اضافه کنید:

```ini
[Service]
ExecStart=
ExecStart=/opt/Throne/Throne --headless --data-dir=%S/throne --debug
```

این یونیت Throne را با root اجرا می‌کند. [دسترسی‌ها در Linux](#linux-privileges) این روش را با روش‌های دیگر اجرا مقایسه می‌کند.

## نصب‌های دیگر Linux {#other-linux}

[اسکریپت نصب](@/get_started/installation.fa.md#linux-script)، Throne را در `/opt/Throne` قرار می‌دهد و [ZIP قابل‌حمل](@/get_started/installation.fa.md#linux-zip) هر جا که آن را استخراج کنید قرار می‌گیرد. هیچ‌کدام یونیتی نصب نمی‌کنند. یونیت را خودتان نصب کنید:

```bash
curl -fsSLo throne.service https://raw.githubusercontent.com/throneproj/Throne/dev/dist/linux/throne.service
sudo install -m 644 throne.service /etc/systemd/system/throne.service
sudo systemctl daemon-reload
sudo systemctl enable --now throne
```

این یونیت `/opt/Throne/Throne` را اجرا می‌کند. اگر Throne در پوشهٔ دیگری است، این مسیر را با یک drop-in تغییر دهید، همان‌طور که برای بستهٔ AUR در پایین نشان داده شده است. `/etc/throne/throne.env` برای این یونیت اختیاری است. برای داشتن نمونه‌ها، فایل [`dist/linux/throne.env`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne.env) را در آنجا ذخیره کنید.

[بستهٔ AUR](@/get_started/installation.fa.md#aur) جداگانه نگهداری می‌شود و Throne را در `/usr/lib/throne` نصب می‌کند. اگر این بسته یونیتی همراه نداشته باشد، یونیت بالا را نصب کنید و با یک drop-in (`sudo systemctl edit throne`) آن را به این مسیر ارجاع دهید:

```ini
[Service]
ExecStart=
ExecStart=/usr/lib/throne/Throne --headless --data-dir=%S/throne
```

## دسترسی‌ها در Linux {#linux-privileges}

TUN و [کیل‌سوییچ](@/advanced/kill_switch.fa.md) به دسترسی‌های سیستمی نیاز دارند. نمایه‌هایی که [هستهٔ اضافی (Extra Core)](@/advanced/chains.fa.md#extra-core) دارند به یک کاربر عادی نیاز دارند تا آن هسته با آن کاربر اجرا شود، چون Throne هرگز Extra Core را با root اجرا نمی‌کند. نحوهٔ اجرای یونیت را انتخاب کنید:

| نحوهٔ اجرای یونیت | TUN | کیل‌سوییچ | نمایه‌های Extra Core | مناسب برای |
| --- | --- | --- | --- | --- |
| با root (یونیت به همان شکلی که عرضه شده است) | بله | بله | خیر | دروازه‌های شبکه و دستگاه‌های اختصاصی |
| با `throne` و capabilityها (`caps.conf`) | بله | خیر (`unsupported`) | بله | دستگاه پروکسی بدون کیل‌سوییچ |
| با `throne` و یک `ThroneCore` با setuid-root | بله | بله | بله | همه‌چیز، بدون اجرای خود Throne با root |
| با `throne` بدون هیچ چیز دیگر | خیر | خیر | بله | فقط پروکسی (ورودی mixed) |

**Capabilityها.** فایل [`caps.conf`](https://github.com/throneproj/Throne/blob/dev/dist/linux/caps.conf) را دانلود کنید، سپس:

```bash
sudo useradd --system --no-create-home --home-dir /var/lib/throne --shell /usr/sbin/nologin throne
sudo install -Dm644 caps.conf /etc/systemd/system/throne.service.d/caps.conf
sudo systemctl daemon-reload && sudo systemctl restart throne
```

systemd در شروع بعدی، مالکیت `/var/lib/throne` را به `throne` می‌دهد. در این حالت `ThroneCore` نباید بیت setuid یا file capability داشته باشد، چون اجرای چنین فایلی capabilityهایی را که یونیت می‌دهد از بین می‌برد.

**هستهٔ setuid.** از یک drop-in استفاده کنید که زیر `[Service]` فقط `User=throne` و `Group=throne` را دارد، و کاری کنید که هسته با root اجرا شود:

```bash
sudo chown root:root /opt/Throne/ThroneCore && sudo chmod u+s /opt/Throne/ThroneCore
```

ارتقای بسته، `ThroneCore` را با فایلی بدون بیت setuid جایگزین می‌کند. در Debian و Ubuntu، این فرمان بیت را در ارتقاها حفظ می‌کند:

```bash
sudo dpkg-statoverride --update --add root root 4755 /opt/Throne/ThroneCore
```

`NoNewPrivileges=`، یا گزینهٔ مقاوم‌سازی‌ای را که آن را در بر دارد، به یونیت اضافه نکنید: این کار جلوی هستهٔ setuid را می‌گیرد.

`thronectl status` در `capabilities.tun` و `capabilities.kill_switch` نشان می‌دهد این Throne چه کارهایی می‌تواند انجام دهد. هسته خودش بررسی می‌کند که آیا می‌تواند `/dev/net/tun` را باز کند و `CAP_NET_ADMIN` دارد یا نه، بنابراین یک کانتینر root بدون `NET_ADMIN` به‌جای اینکه بعداً شکست بخورد، `false` گزارش می‌کند.

## یونیت کاربری systemd {#user-unit}

بدون root، Throne به‌صورت یک یونیت کاربری در نشست خود شما اجرا می‌شود. فایل [`throne-user.service`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne-user.service) را دانلود کنید، سپس:

```bash
install -Dm644 throne-user.service ~/.config/systemd/user/throne.service
systemctl --user daemon-reload && systemctl --user enable --now throne
loginctl enable-linger "$USER"   # start at boot, without a login
```

- این یونیت از پوشهٔ داده‌های برنامهٔ دسکتاپ، یعنی `~/.config/Throne`، استفاده می‌کند، بنابراین همان نمایه‌ها و تنظیمات را دارد. ابتدا از برنامهٔ دسکتاپ خارج شوید. تا وقتی یونیت در حال اجراست، برنامهٔ دسکتاپ می‌گوید که Throne در پس‌زمینه در حال اجراست و خارج می‌شود. پیش از باز کردن برنامهٔ دسکتاپ، `systemctl --user stop throne` را اجرا کنید.
- TUN و کیل‌سوییچ به هستهٔ setuid-root نیاز دارند. برنامهٔ دسکتاپ نخستین باری که دسترسی درخواست می‌کند آن را تنظیم می‌کند ([دسترسی‌ها](@/guides/tun_mode.fa.md#privileges-linux) را ببینید)، و `Throne --check` فرمان‌های لازم را چاپ می‌کند.
- وقتی `Throne --health`، `Throne --print-connection` و `Throne --check` را با کاربر خودتان اجرا کنید، این Throne را بدون نیاز به گزینه پیدا می‌کنند.

## Docker {#docker}

ایمیج `ghcr.io/throneproj/throne:<version>` (و همچنین `latest`) برای `linux/amd64` و `linux/arm64` ساخته می‌شود. این ایمیج Throne را در `/opt/Throne`، `thronectl` را در `/usr/local/bin` و `tini` را به‌عنوان init در خود دارد. داده‌هایش را در volume با مسیر `/var/lib/throne` نگه می‌دارد، برای راه‌اندازی دوباره با کد 75 خارج می‌شود و از `Throne --health` برای بررسی سلامت استفاده می‌کند.

کار را با [`compose.yaml`](https://github.com/throneproj/Throne/blob/dev/docker/compose.yaml) موجود در مخزن Throne شروع کنید. این فایل از تگ `latest` استفاده می‌کند؛ برای اینکه طبق برنامهٔ زمانی خودتان به‌روزرسانی کنید، تگ یک نسخهٔ منتشرشده را در آن بگذارید.

```bash
curl -fsSLO https://raw.githubusercontent.com/throneproj/Throne/dev/docker/compose.yaml
docker compose up -d
docker compose exec throne thronectl --key-file /var/lib/throne/config/api.key status
```

بخش‌های مهم این فایل:

```yaml
services:
  throne:
    image: ghcr.io/throneproj/throne:latest   # or a release tag
    restart: unless-stopped
    stop_grace_period: 45s
    cap_add: [NET_ADMIN]                      # Tun and the kill switch
    devices: ["/dev/net/tun:/dev/net/tun"]    # Tun and the kill switch
    volumes: ["throne-data:/var/lib/throne"]
    ports: ["2080:2080"]                      # the mixed (HTTP and SOCKS) inbound
volumes:
  throne-data:
```

نکته‌ها:

- **پورت پروکسی.** ورودی mixed به‌طور پیش‌فرض روی `127.0.0.1` گوش می‌دهد که یک پورت منتشرشده (published port) به آن نمی‌رسد. `thronectl settings set inbound_address=0.0.0.0` را اجرا کنید و نمایه را دوباره اجرا کنید. پورت‌های منتشرشدهٔ Docker فایروال‌های میزبان مانند `ufw` را دور می‌زنند: روی میزبانی که از اینترنت قابل دسترسی است، فقط نشانی LAN را منتشر کنید، برای مثال `"192.168.1.2:2080:2080"`.
- **TUN و کیل‌سوییچ** به `cap_add: [NET_ADMIN]` و دستگاه `/dev/net/tun` نیاز دارند. بدون آن‌ها، کانتینر همچنان ورودی mixed را ارائه می‌دهد.
- **شبکهٔ میزبان.** با `network_mode: host` (و بدون `ports:`)، TUN و کیل‌سوییچ روی کل میزبان اثر می‌گذارند و فقط یک Throne روی میزبان می‌تواند TUN را اجرا کند. [اجرا در کنار یک Throne دسکتاپ](@/headless/_index.fa.md#beside-desktop) را ببینید.
- **توقف.** `stop_grace_period: 45s` را نگه دارید. مقدار پیش‌فرض Docker، یعنی 10 ثانیه، ممکن است Throne را پیش از آنکه هسته‌اش را متوقف کند به‌اجبار ببندد (kill). با `restart: unless-stopped`، Docker پس از هر خروج، حتی پس از `thronectl shutdown`، Throne را دوباره شروع می‌کند؛ آن را با `docker compose stop` متوقف کنید.
- **سلامت.** Docker فقط کانتینر ناسالم را علامت‌گذاری می‌کند. آن را دوباره راه‌اندازی نمی‌کند.
- **محدودیت‌ها.** نمایه‌های Extra Core اجرا نمی‌شوند، چون کانتینر با root اجرا می‌شود. در Docker بدون root (rootless) و دیگر کانتینرهای دارای user namespace، کیل‌سوییچ با `unsupported` شکست می‌خورد ([Throne چگونه در دسترس می‌ماند](@/headless/recovery.fa.md#protection) را ببینید).
- **گزینه‌ها.** متغیرها را زیر `environment:` بگذارید، یا پرچم‌ها (flags) را با `command:` اضافه کنید که Docker آن‌ها را پس از `--headless` می‌گذارد. پوشهٔ داده‌ها را فقط با `THRONE_DATA_DIR` تغییر دهید، چون بررسی سلامت Throne را از طریق آن پیدا می‌کند.
- **thronectl بدون `--key-file`.** `THRONE_KEY_FILE: /var/lib/throne/config/api.key` را زیر `environment:` اضافه کنید تا `docker compose exec throne thronectl status` بدون تغییر کار کند. خود Throne این متغیر را نادیده می‌گیرد.
- **وارد کردن یک فایل.** آن را با `-T` از طریق pipe بفرستید: `docker compose exec -T throne thronectl import - < links.txt`.
- **API از بیرون کانتینر.** API روی loopback کانتینر گوش می‌دهد. برای دسترسی به آن از شبکه، `THRONE_API_LISTEN: "*:9095"` را همراه با یک فهرست `THRONE_API_ALLOW` تنظیم کنید و پورت 9095 را منتشر کنید. در این صورت درخواست‌های مدیریتی، مانند وارد کردن و تنظیمات، به `THRONE_API_ADMIN_LAN: "1"` هم نیاز دارند، چون هر درخواستی که از طریق یک پورت منتشرشده می‌رسد از بیرون کانتینر می‌آید. پشت NAT در Docker همهٔ کلاینت‌ها یک نشانی مشترک دارند، بنابراین یک کلاینت با کلید قدیمی، دسترسی بقیه را به مدت یک دقیقه قطع می‌کند.
- **گزارش‌ها:** `docker compose logs -f throne`.
- **رابط وب.** `THRONE_WEB_LISTEN: "*:9096"` را تنظیم کنید و پورت 9096 را منتشر کنید تا کانتینر [رابط وب](@/headless/web.fa.md#docker) را هم ارائه دهد.

## macOS {#macos}

یک LaunchDaemon، Throne را هنگام بوت با root اجرا می‌کند. TUN و کیل‌سوییچ کار می‌کنند؛ نمایه‌های Extra Core کار نمی‌کنند. ابتدا `Throne.app` را به `/Applications` منتقل کنید، فایل [`moe.Throne.headless.plist`](https://github.com/throneproj/Throne/blob/dev/dist/macos/moe.Throne.headless.plist) را دانلود کنید، سپس:

```bash
sudo mkdir -p /Library/Logs/Throne
sudo install -m 644 -o root -g wheel moe.Throne.headless.plist /Library/LaunchDaemons/
sudo xattr -dr com.apple.quarantine /Applications/Throne.app   # unsigned builds only
sudo launchctl bootstrap system /Library/LaunchDaemons/moe.Throne.headless.plist
```

- برای متوقف کردن آن و خارج کردنش از launchd (unload): `sudo launchctl bootout system/moe.Throne.headless`.
- داده‌هایش در `/Library/Application Support/Throne` و خروجی‌اش در `/Library/Logs/Throne/throne.log` است.
- فایل plist خود فایل برنامه را مستقیماً اجرا می‌کند، بنابراین launchd پردازه را دنبال می‌کند. هرگز Throne بدون رابط گرافیکی را با `open` اجرا نکنید: این فرمان آن را به LaunchServices می‌سپارد و بی‌درنگ برمی‌گردد.
- launchd، Throne را 10 ثانیه پس از خروج با خطا دوباره شروع می‌کند. launchd نمی‌تواند کدهای خروج خاصی را مستثنا کند، بنابراین خروج‌هایی را هم که یک شروع دوباره برطرفشان نمی‌کند هر 10 ثانیه دوباره امتحان می‌کند: Throne دیگری که از پوشهٔ داده‌ها استفاده می‌کند (کد 3)، یک گزینهٔ نادرست (کد 64) و یک خطای پیکربندی (کد 78). وقتی Throne پشت سر هم شروع می‌شود و خارج می‌شود، گزارش را بخوانید.
- برای دیدن وضعیت آن: `sudo /Applications/Throne.app/Contents/MacOS/Throne --health`.

## سرویس Windows {#windows}

`Throne.exe --headless --service` به‌صورت یک سرویس بومی Windows با حساب LocalSystem اجرا می‌شود، بنابراین TUN و کیل‌سوییچ کار می‌کنند.

1. Throne را با نصب‌کننده برای همهٔ کاربران، در `Program Files`، نصب کنید. سرویس نباید برنامه‌ای را اجرا کند که یک کاربر عادی بتواند آن را جایگزین کند، بنابراین از نصب مخصوص یک کاربر یا یک ZIP در پوشهٔ کاربری‌تان استفاده نکنید.
2. فایل‌های [`install-service.bat`](https://github.com/throneproj/Throne/blob/dev/dist/windows/install-service.bat) و [`uninstall-service.bat`](https://github.com/throneproj/Throne/blob/dev/dist/windows/uninstall-service.bat) را دانلود کنید.
3. از یک Command Prompt با دسترسی مدیر، این را اجرا کنید:

   ```bat
   install-service.bat
   ```

   اگر Throne در پوشهٔ دیگری است، مسیر را بدهید: `install-service.bat "D:\Apps\Throne\Throne.exe"`.

این اسکریپت سرویس `Throne` را می‌سازد، آن را پس از بوت (با شروع تأخیری) اجرا می‌کند و اقدامات بازیابی (recovery actions) آن را تنظیم می‌کند:

```bat
sc.exe failure Throne reset= 86400 actions= restart/3000/restart/10000/restart/60000
sc.exe failureflag Throne 1
```

- `failureflag 1` باعث می‌شود Windows اقدامات بازیابی را وقتی Throne با کد خطا خارج می‌شود هم اعمال کند، نه فقط پس از کرش. به این ترتیب درخواست‌های راه‌اندازی دوباره (کد 75) و خروج‌های watchdog، Throne را برمی‌گردانند. هر کدام یک شکست به حساب می‌آید: در طول یک روز، نخستین راه‌اندازی دوباره پس از 3 ثانیه، دومی پس از 10 ثانیه و بعدی‌ها پس از 60 ثانیه انجام می‌شوند.
- `sc.exe stop Throne` و `thronectl shutdown` آن را به‌طور قطعی متوقف می‌کنند (کد 0). وقتی Windows خاموش می‌شود، تا 45 ثانیه به Throne فرصت می‌دهد.
- پس از توقف با خطا، `sc.exe query Throne` مقدار `WIN32_EXIT_CODE : 1066` و کد خروج خود Throne را به‌صورت `SERVICE_EXIT_CODE` نشان می‌دهد.
- داده‌هایش در `C:\ProgramData\Throne` و گزارشش در `C:\ProgramData\Throne\config\logs\throne.log` است.
- `uninstall-service.bat` سرویس را متوقف و حذف می‌کند. داده‌های `C:\ProgramData\Throne` باقی می‌مانند.
- پیش از به‌روزرسانی Throne، سرویس را متوقف کنید.

یک وظیفهٔ زمان‌بندی‌شده در Task Scheduler جایگزین سرویس نیست: Windows یک وظیفه را با بستن اجباری (hard kill) متوقف می‌کند، بنابراین Throne نمی‌تواند هسته‌اش را به‌درستی متوقف کند، و Windows نمی‌تواند یک وظیفه را بسته به کد خروج آن دوباره شروع کند.

### پوشهٔ داده‌ها {#windows-data-folder}

سرویس، `C:\ProgramData\Throne` را فقط برای SYSTEM و Administrators نگه می‌دارد. پوشه را به همین شکل می‌سازد و در هر بار شروع، این دسترسی را دوباره تنظیم می‌کند، برای فایل‌های درون آن هم. اگر پوشه از قبل وجود داشته باشد اما متعلق به کاربر دیگری باشد، به کاربران دیگر اجازهٔ نوشتن بدهد (پوشه‌ای که خودتان زیر `C:\ProgramData` می‌سازید این‌طور است)، یک link یا junction باشد، یا فایل یا پوشه‌ای را در خود داشته باشد که link یا junction است، سرویس با کد 78 خارج می‌شود و مشکل را در گزارش نام می‌برد. آن پوشه را حذف کنید و سرویس را دوباره شروع کنید. برای یک `--data-dir` که کاربران عادی می‌توانند در آن بنویسند، فقط یک هشدار در گزارش نوشته می‌شود.

### اجرای فرمان‌ها از خط فرمان {#windows-prompt}

`Throne.exe --health`، `--print-connection`، `--check` و `thronectl` را از یک خط فرمان با دسترسی مدیر اجرا کنید و به فرمان‌های `Throne.exe` گزینهٔ `--data-dir C:\ProgramData\Throne` را بدهید. حساب مدیر (administrator) همان LocalSystem نیست، بنابراین پوشهٔ داده‌های پیش‌فرض آن پوشهٔ دیگری است. فقط Administrators و SYSTEM می‌توانند pipe سرویس را باز کنند و فایل کلید آن را بخوانند. از خط فرمانی که دسترسی مدیر ندارد، `--health` پیام `[Instance] Not talking to … access denied` را در گزارش می‌نویسد و با کد 2 خارج می‌شود.

`Throne.exe` یک برنامهٔ گرافیکی (GUI) در Windows است، بنابراین Command Prompt منتظر آن نمی‌ماند. Throne خروجی‌اش را در کنسول برنامه‌ای می‌نویسد که آن را اجرا کرده است. برای اینکه منتظر آن بمانید و کد خروجش را بخوانید:

```bat
start "" /wait "C:\Program Files\Throne\Throne.exe" --health --data-dir C:\ProgramData\Throne
echo %ERRORLEVEL%
```

```powershell
$p = Start-Process 'C:\Program Files\Throne\Throne.exe' -ArgumentList '--health', '--data-dir', 'C:\ProgramData\Throne' -Wait -PassThru
$p.ExitCode
```

`--print-connection` خطوط `export` به سبک POSIX چاپ می‌کند. در PowerShell، مقدارها را خودتان تنظیم کنید:

```powershell
$env:THRONE_ADDR = '127.0.0.1:9095'
$env:THRONE_KEY_FILE = 'C:\ProgramData\Throne\config\api.key'
thronectl status
```

### اجرا از کنسول {#windows-console}

برای آزمایش Throne بدون رابط گرافیکی بدون سرویس:

```bat
start "" /wait "C:\Program Files\Throne\Throne.exe" --headless
```

`Ctrl+C` آن را به‌درستی متوقف می‌کند و `Ctrl+C` دوم هسته را بی‌درنگ می‌بندد (kill). بستن پنجرهٔ کنسول، `taskkill /PID <pid>` بدون `/F`، خروج از حساب کاربری و خاموش کردن سیستم هم آن را به‌درستی متوقف می‌کنند. بیرون از سرویس، پوشهٔ داده‌ها `%LOCALAPPDATA%\Throne` است (وقتی با LocalSystem اجرا شود، `C:\ProgramData\Throne`). راه‌اندازی دوباره یک پردازهٔ جدید Throne شروع می‌کند و با کد 0 خارج می‌شود، بنابراین `start /wait` برمی‌گردد، در حالی که پردازهٔ جدید همچنان در حال اجراست.

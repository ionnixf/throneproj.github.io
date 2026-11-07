+++
title = "نصب و سرویس‌ها"
description = "Throne بدون رابط گرافیکی را به‌صورت سرویس systemd، یونیت کاربری systemd، کانتینر Docker، LaunchDaemon در macOS یا سرویس Windows نصب کنید."
weight = 10
toc = true
+++

Throne را همان‌طور که در [نصب](@/get_started/installation.fa.md) آمده است نصب کنید، سپس اجرای آن را به یک مدیر سرویس بسپارید:

| روش | با چه کاربری اجرا می‌شود | پوشهٔ داده‌ها |
| --- | --- | --- |
| [بستهٔ Linux (یونیت systemd)](#linux-packages) | root | `/var/lib/throne` |
| [یونیت کاربری systemd](#user-unit)، هر توزیع Linux | خودتان | `~/.config/Throne` |
| [Docker](#docker) | root درون کانتینر | volume با مسیر `/var/lib/throne` |
| [LaunchDaemon در macOS](#macos) | root | `/Library/Application Support/Throne` |
| [سرویس Windows](#windows) | LocalSystem | `C:\ProgramData\Throne` |

سپس [thronectl را متصل کنید](@/headless/thronectl.fa.md#connect).

## Debian، Ubuntu، Raspberry Pi OS، Fedora، openSUSE {#linux-packages}

بسته‌های `.deb` و `.rpm` از [صفحهٔ انتشارها](https://github.com/throneproj/Throne/releases) و از مخازن بسته، Throne را در `/opt/Throne` نصب می‌کنند و یونیت systemd با نام `throne.service`، فایل محیطی آن `/etc/throne/throne.env` و `thronectl` را هم نصب می‌کنند. [Linux](@/get_started/installation.fa.md#linux) می‌گوید کدام بسته برای کدام سیستم مناسب است؛ روی Raspberry Pi از نسخهٔ 64 بیتی Raspberry Pi OS استفاده کنید.

```bash
sudo apt install ./Throne-<version>-debian-amd64.deb   # or: sudo dnf install ./Throne-<version>-fedora-amd64.rpm
sudo /opt/Throne/Throne --check                        # optional: can this computer run it?
sudo systemctl enable --now throne
journalctl -u throne -f
```

- بسته هرگز یونیت را فعال نمی‌کند. ارتقای بسته، اگر یونیت در حال اجرا باشد، آن را دوباره راه‌اندازی می‌کند و حذف بسته آن را متوقف و غیرفعال می‌کند.
- `/var/lib/throne` (نمایه‌ها، تنظیمات و کلید API) پس از حذف بسته، حتی با `apt purge`، باقی می‌ماند.
- برای تنظیم یک متغیر، کامنت آن را در `/etc/throne/throne.env` بردارید و `sudo systemctl restart throne` را اجرا کنید. ارتقاها تغییرات شما را نگه می‌دارند. هرگز کلید API را آنجا نگذارید؛ به‌جای آن، `THRONE_API_KEY_FILE` نام یک فایل کلید را مشخص می‌کند.
- برای تغییر خط فرمان، با `sudo systemctl edit throne` یک drop-in اضافه کنید:

  ```ini
  [Service]
  ExecStart=
  ExecStart=/opt/Throne/Throne --headless --data-dir=%S/throne --debug
  ```

- یونیت دوم، `throne-web.service`، [رابط وب](@/headless/web.fa.md#systemd) را اجرا می‌کند. آن هم فعال نمی‌شود.

## نصب‌های دیگر Linux {#other-linux}

[اسکریپت نصب](@/get_started/installation.fa.md#linux-script) و [ZIP قابل‌حمل](@/get_started/installation.fa.md#linux-zip) یونیتی نصب نمی‌کنند و [بستهٔ AUR](@/get_started/installation.fa.md#aur) هم ممکن است یونیتی همراه نداشته باشد. یونیت بسته را نصب کنید:

```bash
curl -fsSLo throne.service https://raw.githubusercontent.com/throneproj/Throne/dev/dist/linux/throne.service
sudo install -m 644 throne.service /etc/systemd/system/throne.service
sudo systemctl daemon-reload && sudo systemctl enable --now throne
```

این یونیت `/opt/Throne/Throne` را اجرا می‌کند، یعنی جایی که اسکریپت نصب Throne را قرار می‌دهد. برای پوشهٔ دیگر، مانند `/usr/lib/throne` در بستهٔ AUR، مسیر را با یک drop-in (`sudo systemctl edit throne`) تغییر دهید:

```ini
[Service]
ExecStart=
ExecStart=/usr/lib/throne/Throne --headless --data-dir=%S/throne
```

برای داشتن نمونهٔ متغیرها، فایل [`dist/linux/throne.env`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne.env) را با نام `/etc/throne/throne.env` ذخیره کنید.

## دسترسی‌ها در Linux {#linux-privileges}

TUN و [کیل‌سوییچ](@/advanced/kill_switch.fa.md) به دسترسی‌های سیستمی نیاز دارند. نمایه‌های [هستهٔ اضافی (Extra Core)](@/advanced/chains.fa.md#extra-core) به یک کاربر عادی نیاز دارند، چون Throne هرگز Extra Core را با root اجرا نمی‌کند. نحوهٔ اجرای یونیت را انتخاب کنید:

| نحوهٔ اجرای یونیت | TUN | کیل‌سوییچ | نمایه‌های Extra Core |
| --- | --- | --- | --- |
| با root (یونیت به همان شکلی که عرضه شده است) | بله | بله | خیر |
| با `throne` و capabilityها (`caps.conf`) | بله | خیر (`unsupported`) | بله |
| با `throne` و یک `ThroneCore` با setuid-root | بله | بله | بله |
| با `throne` بدون هیچ چیز دیگر | خیر | خیر | بله |

**Capabilityها.** فایل [`caps.conf`](https://github.com/throneproj/Throne/blob/dev/dist/linux/caps.conf) را دانلود کنید، سپس:

```bash
sudo useradd --system --no-create-home --home-dir /var/lib/throne --shell /usr/sbin/nologin throne
sudo install -Dm644 caps.conf /etc/systemd/system/throne.service.d/caps.conf
sudo systemctl daemon-reload && sudo systemctl restart throne
```

در این حالت `ThroneCore` نباید بیت setuid یا file capability داشته باشد، چون اجرای چنین فایلی capabilityهایی را که یونیت می‌دهد از بین می‌برد.

**هستهٔ setuid.** از یک drop-in استفاده کنید که زیر `[Service]` فقط `User=throne` و `Group=throne` را دارد، و کاری کنید که هسته با root اجرا شود:

```bash
sudo chown root:root /opt/Throne/ThroneCore && sudo chmod u+s /opt/Throne/ThroneCore
```

ارتقای بسته، `ThroneCore` را با فایلی بدون بیت setuid جایگزین می‌کند. در Debian و Ubuntu، `sudo dpkg-statoverride --update --add root root 4755 /opt/Throne/ThroneCore` بیت را حفظ می‌کند. `NoNewPrivileges=`، یا گزینهٔ مقاوم‌سازی‌ای را که آن را در بر دارد، به یونیت اضافه نکنید: این کار جلوی هستهٔ setuid را می‌گیرد.

`thronectl status` در `capabilities.tun` و `capabilities.kill_switch` نشان می‌دهد این Throne چه کارهایی می‌تواند انجام دهد.

## یونیت کاربری systemd {#user-unit}

بدون root، Throne به‌صورت یک یونیت کاربری در نشست خود شما اجرا می‌شود. فایل [`throne-user.service`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne-user.service) را دانلود کنید، سپس:

```bash
install -Dm644 throne-user.service ~/.config/systemd/user/throne.service
systemctl --user daemon-reload && systemctl --user enable --now throne
loginctl enable-linger "$USER"   # start at boot, without a login
```

- این یونیت از پوشهٔ داده‌های برنامهٔ دسکتاپ، یعنی `~/.config/Throne`، با همان نمایه‌ها و تنظیمات استفاده می‌کند. ابتدا از برنامهٔ دسکتاپ خارج شوید، و پیش از باز کردن دوباره‌اش `systemctl --user stop throne` را اجرا کنید.
- TUN و کیل‌سوییچ به هستهٔ setuid-root نیاز دارند. برنامهٔ دسکتاپ نخستین باری که [دسترسی](@/guides/tun_mode.fa.md#privileges-linux) درخواست می‌کند آن را تنظیم می‌کند، و `Throne --check` فرمان‌های لازم را چاپ می‌کند.
- وقتی `Throne --health`، `--print-connection` و `--check` را با کاربر خودتان اجرا کنید، این Throne را بدون نیاز به گزینه پیدا می‌کنند.

## Docker {#docker}

ایمیج `ghcr.io/throneproj/throne` (تگ یک نسخهٔ منتشرشده، یا `latest`) روی `linux/amd64` و `linux/arm64` اجرا می‌شود و `thronectl` را در `/usr/local/bin` دارد. کار را با [`compose.yaml`](https://github.com/throneproj/Throne/blob/dev/docker/compose.yaml) موجود در مخزن Throne شروع کنید:

```bash
curl -fsSLO https://raw.githubusercontent.com/throneproj/Throne/dev/docker/compose.yaml
docker compose up -d
docker compose exec throne thronectl --key-file /var/lib/throne/config/api.key status
```

بخش‌های اصلی آن:

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

- **پورت پروکسی.** ورودی mixed روی `127.0.0.1` گوش می‌دهد که یک پورت منتشرشده (published port) به آن نمی‌رسد: `thronectl settings set inbound_address=0.0.0.0` را اجرا کنید و نمایه را دوباره اجرا کنید. پورت‌های منتشرشده فایروال‌های میزبان مانند `ufw` را دور می‌زنند؛ روی میزبانی که از اینترنت قابل دسترسی است، فقط نشانی LAN را منتشر کنید (`"192.168.1.2:2080:2080"`).
- **شبکهٔ میزبان** (`network_mode: host`، بدون `ports:`): TUN و کیل‌سوییچ روی کل میزبان اثر می‌گذارند و فقط یک Throne آنجا می‌تواند TUN را اجرا کند.
- **توقف.** `stop_grace_period: 45s` را نگه دارید، وگرنه Docker ممکن است Throne را پیش از آنکه هسته‌اش را متوقف کند به‌اجبار ببندد (kill). `restart: unless-stopped` حتی پس از `thronectl shutdown` هم Throne را دوباره شروع می‌کند؛ از `docker compose stop` استفاده کنید.
- **سلامت.** بررسی سلامت، `Throne --health` را اجرا می‌کند. Docker فقط کانتینر ناسالم را علامت‌گذاری می‌کند؛ آن را دوباره راه‌اندازی نمی‌کند.
- **محدودیت‌ها.** نمایه‌های Extra Core اجرا نمی‌شوند، چون کانتینر با root اجرا می‌شود. در Docker بدون root (rootless) و دیگر کانتینرهای دارای user namespace، کیل‌سوییچ با `unsupported` شکست می‌خورد.
- **گزینه‌ها.** متغیرها زیر `environment:` و پرچم‌ها (flags) در `command:` قرار می‌گیرند (پس از `--headless` افزوده می‌شوند). پوشهٔ داده‌ها را فقط با `THRONE_DATA_DIR` تغییر دهید، چون بررسی سلامت از آن استفاده می‌کند.
- **thronectl.** `THRONE_KEY_FILE: /var/lib/throne/config/api.key` را زیر `environment:` بگذارید تا به `--key-file` نیازی نباشد. یک فایل را با `docker compose exec -T throne thronectl import - < links.txt` وارد کنید.
- **API از شبکه.** `THRONE_API_LISTEN: "*:9095"` و `THRONE_API_ALLOW` را تنظیم کنید، پورت 9095 را منتشر کنید، و برای وارد کردن و تنظیمات `THRONE_API_ADMIN_LAN: "1"` را هم اضافه کنید، چون درخواست‌هایی که از طریق یک پورت منتشرشده می‌رسند از بیرون کانتینر می‌آیند. پشت NAT در Docker همهٔ کلاینت‌ها یک نشانی مشترک دارند، بنابراین یک کلاینت با کلید قدیمی، دسترسی بقیه را به مدت یک دقیقه قطع می‌کند.
- **گزارش‌ها:** `docker compose logs -f throne`. برای رابط وب، [Docker](@/headless/web.fa.md#docker) را ببینید.

## macOS {#macos}

یک LaunchDaemon، Throne را هنگام بوت با root اجرا می‌کند، با TUN و کیل‌سوییچ اما بدون نمایه‌های Extra Core. `Throne.app` را به `/Applications` منتقل کنید، فایل [`moe.Throne.headless.plist`](https://github.com/throneproj/Throne/blob/dev/dist/macos/moe.Throne.headless.plist) را دانلود کنید، سپس:

```bash
sudo mkdir -p /Library/Logs/Throne
sudo install -m 644 -o root -g wheel moe.Throne.headless.plist /Library/LaunchDaemons/
sudo xattr -dr com.apple.quarantine /Applications/Throne.app   # unsigned builds only
sudo launchctl bootstrap system /Library/LaunchDaemons/moe.Throne.headless.plist
```

- برای متوقف کردن آن و خارج کردنش از launchd (unload): `sudo launchctl bootout system/moe.Throne.headless`.
- گزارش در `/Library/Logs/Throne/throne.log` است. برای دیدن وضعیت، `sudo /Applications/Throne.app/Contents/MacOS/Throne --health` را اجرا کنید.
- هرگز Throne بدون رابط گرافیکی را با `open` اجرا نکنید: این فرمان بی‌درنگ برمی‌گردد و launchd پردازه را گم می‌کند.
- launchd، Throne را 10 ثانیه پس از هر خروج با خطا دوباره شروع می‌کند، حتی پس از خطاهایی که یک شروع دوباره برطرفشان نمی‌کند (کدهای 3، 64 و 78). وقتی Throne پشت سر هم شروع می‌شود و خارج می‌شود، گزارش را بخوانید.

## سرویس Windows {#windows}

`Throne.exe --headless --service` به‌صورت یک سرویس Windows با حساب LocalSystem اجرا می‌شود، بنابراین TUN و کیل‌سوییچ کار می‌کنند.

1. Throne را با نصب‌کننده برای همهٔ کاربران، در `Program Files`، نصب کنید. سرویس نباید برنامه‌ای را اجرا کند که یک کاربر عادی بتواند آن را جایگزین کند، بنابراین از نصب مخصوص یک کاربر یا یک ZIP در پوشهٔ کاربری‌تان استفاده نکنید.
2. فایل‌های [`install-service.bat`](https://github.com/throneproj/Throne/blob/dev/dist/windows/install-service.bat) و [`uninstall-service.bat`](https://github.com/throneproj/Throne/blob/dev/dist/windows/uninstall-service.bat) را دانلود کنید.
3. از یک Command Prompt با دسترسی مدیر، `install-service.bat` را اجرا کنید، یا اگر Throne در پوشهٔ دیگری است، `install-service.bat "D:\Apps\Throne\Throne.exe"` را.

سرویس `Throne` پس از بوت (با شروع تأخیری) اجرا می‌شود. اقدامات بازیابی (recovery actions) آن، Throne را پس از 3، 10 و سپس 60 ثانیه دوباره شروع می‌کنند، حتی پس از خروج با کد خطا، مانند درخواست راه‌اندازی دوباره (کد 75) یا خروج watchdog.

- `sc.exe stop Throne` و `thronectl shutdown` آن را به‌طور قطعی متوقف می‌کنند. پیش از به‌روزرسانی Throne، سرویس را متوقف کنید.
- پس از خطا، `sc.exe query Throne` کد خروج خود Throne را به‌صورت `SERVICE_EXIT_CODE` نشان می‌دهد (در این حالت `WIN32_EXIT_CODE` برابر `1066` است). گزارش در `C:\ProgramData\Throne\config\logs\throne.log` است.
- `uninstall-service.bat` سرویس را حذف می‌کند و داده‌ها را نگه می‌دارد.

### پوشهٔ داده‌ها {#windows-data-folder}

سرویس، `C:\ProgramData\Throne` را فقط برای SYSTEM و Administrators نگه می‌دارد. اگر پوشه متعلق به کاربر دیگری باشد، به کاربران دیگر اجازهٔ نوشتن بدهد (پوشه‌ای که خودتان زیر `C:\ProgramData` می‌سازید این‌طور است)، یا خودش یا چیزی درون آن یک link یا junction باشد، سرویس با کد 78 خارج می‌شود و مشکل را در گزارش نام می‌برد. پوشه را حذف کنید و سرویس را دوباره شروع کنید.

### اجرای فرمان‌ها از خط فرمان {#windows-prompt}

`Throne.exe --health`، `--print-connection`، `--check` و `thronectl` را از یک خط فرمان با دسترسی مدیر اجرا کنید و به فرمان‌های `Throne.exe` گزینهٔ `--data-dir C:\ProgramData\Throne` را بدهید: حساب مدیر (administrator) همان LocalSystem نیست و پوشهٔ داده‌های پیش‌فرض دیگری دارد. فقط Administrators و SYSTEM می‌توانند pipe سرویس را باز کنند و فایل کلید آن را بخوانند.

`Throne.exe` یک برنامهٔ گرافیکی (GUI) است، بنابراین Command Prompt منتظر آن نمی‌ماند. برای اینکه منتظر بمانید و کد خروجش را بخوانید:

```bat
start "" /wait "C:\Program Files\Throne\Throne.exe" --health --data-dir C:\ProgramData\Throne
echo %ERRORLEVEL%
```

`--print-connection` خطوط `export` به سبک POSIX چاپ می‌کند. در PowerShell، مقدارها را خودتان تنظیم کنید:

```powershell
$env:THRONE_ADDR = '127.0.0.1:9095'
$env:THRONE_KEY_FILE = 'C:\ProgramData\Throne\config\api.key'
thronectl status
```

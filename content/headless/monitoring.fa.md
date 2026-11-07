+++
title = "بررسی سلامت و گزارش‌ها"
description = "بررسی کنید که آیا یک رایانه می‌تواند Throne بدون رابط گرافیکی را اجرا کند، وضعیت Throne در حال اجرا را از خودش بپرسید و گزارش‌هایش را پیدا کنید."
weight = 40
toc = true
+++

## Throne --check {#check}

`Throne --check` به شما می‌گوید که آیا این رایانه می‌تواند Throne بدون رابط گرافیکی را با پیکربندی فعلی اجرا کند. آن را با کاربر Throne و با همان گزینه‌ها و متغیرهایی که Throne با آن‌ها شروع می‌شود اجرا کنید. این فرمان هیچ چیزی را تغییر نمی‌دهد و در کنار یک Throne در حال اجرا هم اجرا می‌شود.

```text
$ sudo /opt/Throne/Throne --check
[INFO] data dir /var/lib/throne (root default): does not exist yet; the first start creates it
[ OK ] port 127.0.0.1:9095 (the Throne API, the headless default): free
...
result: 0 failed, 0 warnings
```

پوشهٔ داده‌ها، تنظیمات، هسته، TUN، کیل‌سوییچ، کلید API، پورت‌ها، حالت راه‌اندازی دوباره و [شناسهٔ دستگاه](@/headless/_index.fa.md#device-id) را بررسی می‌کند، و هر جا راه‌حلی وجود داشته باشد خطوط `fix:` را هم چاپ می‌کند. فقط چیزی که به‌طور کلی مانع کار کردن Throne شود `[FAIL]` است، و در این صورت فرمان با کد 1 خارج می‌شود. نبود دسترسی‌های لازم برای TUN یا کیل‌سوییچ `[WARN]` است، همراه با فرمان‌های `setcap` یا `chmod u+s` برای رفع آن، یا در کانتینر همراه با `--cap-add NET_ADMIN` و `--device /dev/net/tun`.

## Throne --health {#health}

`Throne --health` از Throne در حال اجرا می‌پرسد و یک خط JSON چاپ می‌کند. آن را با کاربر Throne و برای همان پوشهٔ داده‌ها اجرا کنید:

```bash
$ sudo /opt/Throne/Throne --health
{"api":"listening","core_restarts":0,"degraded":null,"headless_dialogs":0,"ok":true,"previous_crash":false,"previous_exit":{"at":1760700000,"code":75,"reason":"restart requested"},"ready":true,"state":"running","stuck_s":0}
```

وقتی Throne سالم باشد، با کد 0 خارج می‌شود؛ برای کدهای دیگر، [کدهای خروج](@/headless/command_line.fa.md#exit-codes) را ببینید. ایمیج Docker از آن برای بررسی سلامت استفاده می‌کند.

| فیلد | معنی |
| --- | --- |
| `ok` | Throne شروع شده است، هسته متصل است، هیچ چیزی گیر نکرده است، و کیل‌سوییچ خاموش است یا کار می‌کند. این مقدار هرگز به یک سرور راه‌دور یا به نمایهٔ در حال اجرا بستگی ندارد، بنابراین راه‌اندازی دوبارهٔ Throne در زمانی که این مقدار `false` است، هنگام از کار افتادن یک سرور به چرخهٔ بی‌پایان نمی‌افتد. |
| `ready` | Throne شروع به کار را تمام کرده است. |
| `state` | `starting`، `idle`، `connecting`، `running` یا `stopping`. |
| `degraded` | `null`، یا دلیل اینکه Throne نمی‌تواند با پیکربندی فعلی کار کند (جدول زیر را ببینید). |
| `stuck_s` | چند ثانیه است که یک نمایه، فراتر از 90 ثانیه، در حال اتصال است، یا فراتر از 45 ثانیه، در حال توقف است. تا وقتی بیشتر از 0 است، `ok` برابر `false` است. |
| `headless_dialogs` | پرسش‌هایی که چون پنجره‌ای وجود ندارد نادیده گرفته شده‌اند. این مقدار باید 0 بماند؛ گزارش آن‌ها را در خطوط `HEADLESS-DIALOG` نام می‌برد. |
| `previous_exit` | اینکه اجرای قبلی چگونه پایان یافت، به‌صورت `{code, reason, at}`، یا `null`. اگر به‌درستی پایان نیافته باشد، `previous_crash` برابر `true` است. |
| `api` | شنوندهٔ API: `off`، `listening`، `key_invalid`، `failed`، `retrying` (تا 30 ثانیه برای نشانی‌اش صبر می‌کند) یا `address_missing`. |

مقدارهای `degraded`، به ترتیبی که Throne وقتی چند مورد صدق کنند گزارش می‌دهد:

| مقدار | معنی |
| --- | --- |
| `core_gave_up` | هسته پشت سر هم شکست خورد. Throne با کد 70 خارج می‌شود، یا پس از سه شروع پیاپی از این دست، [روشن می‌ماند](@/headless/recovery.fa.md#protection) و هر 5 دقیقه هسته را دوباره امتحان می‌کند. |
| `core_unavailable` | پردازهٔ هسته در حال اجرا نیست. |
| `kill_switch_failed` | کیل‌سوییچ روشن است اما شکست خورده است، بنابراین نمایه‌ها رد می‌شوند. [کیل‌سوییچ](@/advanced/kill_switch.fa.md) را ببینید. |
| `api_unavailable` | API روشن است اما گوش نمی‌دهد، برای مثال چون برنامهٔ دیگری پورت آن را در اختیار دارد یا فایل کلیدش قابل استفاده نیست. `ok` همچنان `true` می‌ماند. |
| `tun_unavailable` | TUN وقتی Throne آخرین بار متوقف شد روشن بود و اکنون شروع نمی‌شود: Throne دسترسی‌های لازم را ندارد، یا چیز دیگری TUN را در اختیار دارد ([تداخل TUN](@/headless/_index.fa.md#beside-desktop)). نمایه اجرا نشد. `thronectl tun off` و سپس `thronectl connect` را اجرا کنید. |
| `resume_suppressed` | Throne آخرین نمایه را دوباره اجرا نکرد، چون در هر یک از سه بار آخری که این کار را کرد، Throne یا هسته‌اش در کمتر از یک دقیقه متوقف شد. نمایه را با `thronectl connect` اجرا کنید. |
| `vpn_auth_failed` | سرور یک نمایهٔ VPN اطلاعات ورود ذخیره‌شدهٔ آن را رد کرد. وقتی نمایه متوقف شود، این وضعیت پاک می‌شود. |
| `vpn_challenge_pending` | سرور یک نمایهٔ VPN چیزی می‌خواهد که Throne خودش نمی‌تواند به آن پاسخ دهد، مانند ورود به حساب از طریق وب. Throne حداکثر پس از 5 دقیقه آن را لغو می‌کند. |

[مشخصات API](https://github.com/throneproj/Throne-api/blob/main/spec/api.md#headless-throne) جزئیات را دارد. برای فیلدهای بیشتر، مانند `pid`، `version` و فیلدهای `thronectl status`، یک اسکریپت می‌تواند `HEALTH` را به سوکت `config/.instance` در پوشهٔ داده‌ها بفرستد (نه در Windows):

```bash
echo HEALTH | sudo socat -t 3 - UNIX-CONNECT:/var/lib/throne/config/.instance
```

## systemd: آمادگی، وضعیت و watchdog {#systemd}

یونیت عرضه‌شده از نوع `Type=notify` است:

- **`READY=1`** وقتی Throne پوشهٔ داده‌ها را در اختیار گرفته است، پایگاه‌داده باز است، API گوش می‌دهد، هسته متصل است و وضعیت کیل‌سوییچ مشخص شده است؛ معمولاً در کمتر از 20 ثانیه. تا آن زمان Throne مهلت شروع را تمدید می‌کند، بنابراین `TimeoutStartSec=90` فقط شروعی را که گیر کرده است پایان می‌دهد.
- **`STATUS=`** که `systemctl status` آن را نشان می‌دهد، مانند `Idle`، `Connected <profile> (Tun)` یا `Degraded: <reason>`.
- **`WATCHDOG=1`** هر 20 ثانیه (`WatchdogSec=60`). اگر حلقهٔ اصلی گیر کند، systemd سیگنال `SIGABRT` را می‌فرستد و Throne را دوباره شروع می‌کند.

`KillMode=mixed` الزامی است: systemd سیگنال `SIGTERM` را فقط به Throne می‌فرستد، و Throne هسته و کیل‌سوییچ را به‌ترتیب متوقف می‌کند و سپس خارج می‌شود (`TimeoutStopSec=45`). `SIGTERM` دوم هسته را بی‌درنگ می‌بندد. از `systemctl kill` استفاده نکنید: این فرمان مستقیماً به هسته و برنامهٔ کمکی کیل‌سوییچ سیگنال می‌فرستد.

## گزارش‌ها {#logs}

Throne گزارش خود را در خروجی خطای استاندارد (standard error) و در `config/logs/throne.log` درون پوشهٔ داده‌ها می‌نویسد:

| نحوهٔ اجرای Throne | گزارش |
| --- | --- |
| یونیت سیستمی systemd | `journalctl -u throne -f` |
| یونیت کاربری systemd | `journalctl --user -u throne -f` |
| Docker | `docker compose logs -f throne` |
| LaunchDaemon در macOS | `/Library/Logs/Throne/throne.log` |
| سرویس Windows | `C:\ProgramData\Throne\config\logs\throne.log` |

`thronectl logs -n 500` آخرین 500 خط را از طریق API چاپ می‌کند، و `thronectl logs -f` خطوط جدید را هم دنبال می‌کند، حتی وقتی Throne دوباره راه‌اندازی می‌شود. این یک [درخواست مدیریتی](@/headless/thronectl.fa.md#remote) است. Throne جدیدترین 5000 خط را، همراه با خروجی هسته، نگه می‌دارد؛ صفحهٔ `Logs` در [رابط وب](@/headless/web.fa.md) هم آن‌ها را نشان می‌دهد.

`--debug` خطوط debug را اضافه می‌کند. `--log-format` قالب خروجی خطای استاندارد را تعیین می‌کند: `journal` (اولویت‌های syslog برای journald)، `plain` (زمان محلی)، یا `auto` که وقتی خروجی خطای استاندارد به journal می‌رود `journal` را انتخاب می‌کند.

خطوطی که ارزش جست‌وجو دارند:

- `EXIT <code>: <reason>`: چرا Throne خارج شد. [کدهای خروج](@/headless/command_line.fa.md#exit-codes) را ببینید.
- `Ready <n> ms after launch`: شروع به کار تمام شده است.
- `[Resume] …`: اینکه آیا آخرین نمایه دوباره اجرا شد یا نه.
- `[Kill switch] Not active (<code>): …`: چرا کیل‌سوییچ کار نمی‌کند.
- `HEADLESS-DIALOG`: پرسشی که کسی نتوانست به آن پاسخ دهد.

وقتی یک اجرا به‌درستی پایان نیابد، گزارش آن با نام `config/logs/crashed-<time>.log` نگه داشته می‌شود و گزارش‌های کرش به `config/crashes/` می‌روند.

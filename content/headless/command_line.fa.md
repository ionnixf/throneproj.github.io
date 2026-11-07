+++
title = "خط فرمان و پوشهٔ داده‌ها"
description = "گزینه‌ها، متغیرهای محیطی، پوشهٔ داده‌ها، حالت‌های راه‌اندازی دوباره و کدهای خروج Throne بدون رابط گرافیکی."
weight = 30
toc = true
+++

گزینه‌های برنامهٔ دسکتاپ در [خط فرمان](@/reference/files.fa.md#command-line) آمده‌اند.

## گزینه‌ها و متغیرها {#options}

`Throne --headless --help` گزینه‌ها را فهرست می‌کند. گزینه بر متغیر متناظرش اولویت دارد. با یونیت systemd، متغیرها را در `/etc/throne/throne.env` و گزینه‌ها را با یک drop-in تنظیم کنید ([بسته‌های Linux](@/headless/installation.fa.md#linux-packages) را ببینید).

| گزینه | متغیر | کاری که انجام می‌دهد |
| --- | --- | --- |
| `--headless` | `THRONE_HEADLESS=1` | بدون پنجره اجرا می‌شود. |
| `--data-dir DIR` | `THRONE_DATA_DIR` | [پوشهٔ داده‌ها](#data-dir). |
| `--restart-mode MODE` | `THRONE_RESTART_MODE` | `auto` (پیش‌فرض)، `exit` یا `exec`. [حالت‌های راه‌اندازی دوباره](#restart-modes) را ببینید. |
| `--no-resume` | `THRONE_RESUME=0` | آخرین نمایه را هنگام شروع اجرا نمی‌کند. |
| `--no-kill-switch` | `THRONE_KILL_SWITCH=0` | [کیل‌سوییچ](@/advanced/kill_switch.fa.md) را در این اجرا خاموش نگه می‌دارد. نمایه‌ها تا وقتی تنظیم `kill_switch` خاموش نشود رد می‌شوند. برای [بازیابی دسترسی](@/headless/recovery.fa.md#recovery). |
| `--log-format FORMAT` | `THRONE_LOG_FORMAT` | `auto` (پیش‌فرض)، `journal` یا `plain`. [گزارش‌ها](@/headless/monitoring.fa.md#logs) را ببینید. |
| `--api-listen LISTEN` | `THRONE_API_LISTEN` | `PORT` (روی `127.0.0.1`)، `IPv4:PORT`، `[IPv6]:PORT`، `*:PORT` یا `off`. پیش‌فرض: `127.0.0.1:9095`، یا آنچه تنظیمات ذخیره‌شده می‌گویند. |
| `--api-key-file FILE` | `THRONE_API_KEY_FILE` | کلید API را از این فایل موجود می‌خواند (`thronectl keygen -o FILE` چنین فایلی می‌سازد). پیش‌فرض: `config/api.key` در پوشهٔ داده‌ها. |
| `--api-admin-lan` | `THRONE_API_ADMIN_LAN=1` | به [درخواست‌های مدیریتی](@/headless/thronectl.fa.md#remote) از شبکه هم پاسخ می‌دهد. |
| `--api-allow LIST` | `THRONE_API_ALLOW` | نشانی‌ها و زیرشبکه‌هایی که می‌توانند از شنونده‌ای (listener) که روی loopback نیست استفاده کنند، جداشده با ویرگول یا فاصله. اگر تنظیم نشود همهٔ نشانی‌ها مجازند؛ فهرست خالی باعث خروج با کد 64 می‌شود. |
| `--debug` | | گزارش‌گیری در سطح debug. |
| `--service` | | فقط Windows: زیر Service Control Manager اجرا می‌شود. |
| `--health` | | از Throne در حال اجرا [وضعیتش را می‌پرسد](@/headless/monitoring.fa.md#health). |
| `--print-connection` | | `THRONE_ADDR` و `THRONE_KEY_FILE` را برای [thronectl](@/headless/thronectl.fa.md#connect) چاپ می‌کند. |
| `--check` | | [بررسی می‌کند](@/headless/monitoring.fa.md#check) که آیا این رایانه می‌تواند Throne بدون رابط گرافیکی را اجرا کند. |

- `THRONE_RESUME`، `THRONE_KILL_SWITCH` و `THRONE_API_ADMIN_LAN` این مقدارها را می‌پذیرند: `1`، `true`، `yes`، `on`، `0`، `false`، `no` یا `off`. هر مقدار دیگری باعث خروج با کد 64 می‌شود.
- گزینه‌ها و متغیرهای `--api-*` در هر بار شروع بر تنظیمات ذخیره‌شدهٔ API اولویت دارند و هرگز ذخیره نمی‌شوند، بنابراین یک تنظیم نادرست API همیشه از خط فرمان قابل اصلاح است.
- `--health`، `--print-connection` و `--check` بدون `--headless` کار می‌کنند. هر بار فقط از یکی از آن‌ها استفاده کنید، و هرگز همراه با `--service`.
- لینک‌ها و فایل‌ها در خط فرمان باعث خروج با کد 64 می‌شوند. آن‌ها را با [`thronectl import`](@/headless/thronectl.fa.md#profiles) وارد کنید.

## پوشهٔ داده‌ها {#data-dir}

Throne بدون رابط گرافیکی از نخستین موردی از این فهرست که صدق کند استفاده می‌کند:

1. `--data-dir`، سپس `THRONE_DATA_DIR`.
2. `STATE_DIRECTORY` که systemd آن را برای `StateDirectory=` تنظیم می‌کند.
3. با root: `/var/lib/throne` در Linux، `/Library/Application Support/Throne` در macOS. در Windows، به‌صورت سرویس یا با LocalSystem: `C:\ProgramData\Throne`.
4. پوشهٔ برنامهٔ دسکتاپ: `~/.config/Throne` در Linux، `~/Library/Preferences/Throne` در macOS، `%LOCALAPPDATA%\Throne` در Windows.

هرگز از پوشهٔ یک نصب قابل‌حمل در کنار برنامه استفاده نمی‌کند؛ آن پوشه را با `--data-dir` بدهید.

در Linux و macOS، Throne پوشه‌های ناموجود را طوری می‌سازد که فقط کاربر خودش بتواند آن‌ها را بخواند. وقتی با root اجرا شود، پوشهٔ داده‌هایی را که کاربران دیگر می‌توانند در آن بنویسند رد می‌کند (کد 78)، چون تنظیمات، برنامه‌هایی را نام می‌برند که Throne اجرا می‌کند. برای Windows، [پوشهٔ داده‌ها](@/headless/installation.fa.md#windows-data-folder) را ببینید.

| مسیر | محتوا |
| --- | --- |
| `config/throne.db` | نمایه‌ها، گروه‌ها، نمایه‌های مسیریابی و تنظیمات، مانند برنامهٔ دسکتاپ |
| `config/api.key` | کلید API که فقط کاربر Throne می‌تواند آن را بخواند |
| `config/logs/throne.log` | گزارش این اجرا. `crashed-<time>.log` گزارش اجرایی را نگه می‌دارد که به‌درستی پایان نیافته است. |
| `config/crashes/` | گزارش‌های کرش |
| `config/hwid` | [شناسهٔ دستگاه](@/headless/_index.fa.md#device-id)، وقتی سیستم شناسه‌ای ندارد |
| `web/` | فقط در ایمیج Docker: هش گذرواژه و گواهی TLS [رابط وب](@/headless/web.fa.md#docker) |

## حالت‌های راه‌اندازی دوباره {#restart-modes}

`thronectl restart`، بازیابی پشتیبان و `SIGHUP`، Throne را دوباره راه‌اندازی می‌کنند. `--restart-mode` تعیین می‌کند چگونه:

| حالت | چه اتفاقی می‌افتد |
| --- | --- |
| `exit` | Throne با کد 75 خارج می‌شود و مدیر سرویس آن را دوباره شروع می‌کند. |
| `exec` | Throne در همان پردازه دوباره شروع می‌شود (Linux و macOS). در Windows یک پردازهٔ جدید Throne شروع می‌کند و با کد 0 خارج می‌شود. |
| `auto` | `exit` زیر systemd، launchd یا init یک کانتینر، یا به‌صورت سرویس Windows. در غیر این صورت `exec`، مانند اجرا در ترمینال. |

یونیت‌های عرضه‌شده، فایل plist و ایمیج Docker به `auto` یا `exit` نیاز دارند؛ سرویس Windows همیشه از `exit` استفاده می‌کند. `Throne --check` نشان می‌دهد `auto` کدام را انتخاب می‌کند. در ترمینال، بستن ترمینال به‌جای راه‌اندازی دوباره، Throne را با کد 0 متوقف می‌کند.

## کدهای خروج {#exit-codes}

Throne پیش از خروج `EXIT <code>: <reason>` را در گزارش می‌نویسد.

| کد | معنی |
| --- | --- |
| 0 | متوقف شد: `SIGTERM`، `SIGINT`، `thronectl shutdown`، توقف سرویس Windows یا `Ctrl+C` |
| 1 | شروع به دلیلی ناموفق بود که ممکن است برطرف شود: قفل، پایگاه‌داده، شنوندهٔ IPC هسته، یا نشانی `--api-listen` که در 30 ثانیه ظاهر نشد |
| 3 | Throne دیگری از پوشهٔ داده‌ها استفاده می‌کند |
| 64 | یک گزینه، مقدار گزینه یا متغیر نادرست |
| 70 | هسته پشت سر هم شکست خورد، یا 60 ثانیه قابل استفاده نبود |
| 71 | یک نمایه 180 ثانیه در حال اتصال یا 90 ثانیه در حال توقف بود |
| 75 | راه‌اندازی دوباره درخواست شد، در حالت `exit` یا به‌صورت سرویس Windows |
| 78 | خطای پیکربندی: پوشهٔ داده‌ها (دسترسی‌های ناامن، یا نبود `HOME` برای پیدا کردن آن)، فایل کلید API، شنوندهٔ `--api-listen`، یا فایل پایگاه‌داده‌ای که قابل استفاده نیست |

کاری که فایل‌های سرویس عرضه‌شده انجام می‌دهند:

- **یونیت systemd:** پس از کدهای 1، 70، 71 و 75، Throne را 3 ثانیه بعد دوباره شروع می‌کند و پس از 0، 3، 64 و 78 آن را خاموش می‌گذارد. هیچ محدودیت شروعی ندارد (`StartLimitIntervalSec=0`): هر راه‌اندازی دوباره، بازیابی و `SIGHUP` یک شروع جدید است، و یک محدودیت می‌توانست Throne را بدون API خاموش رها کند.
- **Docker** (`restart: unless-stopped`): پس از هر خروج آن را دوباره شروع می‌کند، مگر با `docker stop`.
- **فایل plist در launchd:** پس از هر کدی به‌جز 0، حداکثر هر 10 ثانیه یک بار، آن را دوباره شروع می‌کند؛ بنابراین 3، 64 و 78 را هم دوباره امتحان می‌کند.
- **سرویس Windows:** پس از هر کدی به‌جز 0، [اقدامات بازیابی](@/headless/installation.fa.md#windows) خود را اجرا می‌کند.

پس از سه شروع پیاپی بدون هستهٔ کارا، Throne به‌جای خروج با 70 یا 71، [روشن می‌ماند](@/headless/recovery.fa.md#protection). برای کد 78 مربوط به پایگاه‌داده، در گزارش آمده است که چه باید کرد: یک `config/throne.db` آسیب‌دیده را همراه با فایل‌های `-wal` و `-shm` آن به جای دیگری منتقل کنید، یا یک نسخهٔ کپی را برگردانید. فایل‌های فقط‌خواندنی را برای کاربر Throne قابل نوشتن کنید.

فرمان‌های بررسی کدهای خودشان را دارند، و برای گزینهٔ نادرست با 64 خارج می‌شوند:

| فرمان | 0 | 1 | 2 |
| --- | --- | --- | --- |
| `--health` | سالم | ناسالم، یا هنوز در حال شروع | هیچ Throne‌ای پاسخ نداد، یا خط فرمان Windows دسترسی مدیر ندارد |
| `--check` | هیچ خط `[FAIL]` وجود ندارد | یک خط `[FAIL]` | |
| `--print-connection` | هر دو خط را چاپ کرد | | API خاموش است یا گوش نمی‌دهد، کلید آن در یک فایل نیست، یا خط فرمان Windows دسترسی مدیر ندارد |

`--health` و `--print-connection` وقتی هیچ پوشهٔ داده‌ای پیدا نکنند (بدون `HOME`)، با کد 78 خارج می‌شوند؛ پوشه را با `--data-dir` مشخص کنید.

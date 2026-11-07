+++
title = "محافظت در برابر قطع دسترسی و بازیابی"
description = "اینکه Throne بدون رابط گرافیکی چگونه API خود را در دسترس نگه می‌دارد، و وقتی تنظیمات ذخیره‌شده مانند کیل‌سوییچ یا TUN دسترسی شما را قطع می‌کنند، چگونه دوباره دسترسی پیدا کنید."
weight = 50
toc = true
+++

## Throne چگونه در دسترس می‌ماند {#protection}

- **API همیشه روشن است**، از نخستین اجرا روی `127.0.0.1:9095`. [گزینه‌های](@/headless/command_line.fa.md#options) `--api-*` بر تنظیمات ذخیره‌شدهٔ API اولویت دارند، و بازیابی تنظیمات، تنظیمات API، ورودی، TUN و کیل‌سوییچ این دستگاه را نگه می‌دارد.
- **فایل کلید.** یک `api.key` خالی کلید جدیدی می‌گیرد. هر فایل کلید دیگری که قابل استفاده نباشد API را خاموش نگه می‌دارد: آن را حذف کنید تا شروع بعدی کلید جدیدی بسازد.
- **پورت API.** وقتی برنامهٔ دیگری آن را در اختیار دارد، Throne بدون API به کار ادامه می‌دهد (`api_unavailable`) و هر 30 ثانیه دوباره امتحان می‌کند. وقتی Throne دیگری آن را در اختیار دارد، با `--api-listen PORT` پورت دیگری به این یکی بدهید.
- **ورودهای ناموفق** هرگز loopback را مسدود نمی‌کنند، و همچنین نشانی‌ای را که کلاینتش در 10 دقیقهٔ گذشته وارد شده است.
- **کیل‌سوییچ** همیشه اجازهٔ عبور loopback را می‌دهد، و پاسخ‌های پورت API را روی نشانی‌های دیگر. در Docker بدون root (rootless) و کانتینرهای دیگری با user namespace که `CAP_SETGID` ندارند، به‌صورت بسته (fail closed) شکست می‌خورد (`unsupported`)، و نمایه‌ها تا وقتی تنظیم `kill_switch` را خاموش کنید رد می‌شوند.
- **TUN در Linux** ممکن است پاسخ‌ها به کلاینت‌های راه‌دور API را به تونل ببرد، مگر اینکه `tun_auto_redirect` روشن باشد، و مسیر سخت‌گیرانه (strict route) بدون `tun_ipv6` ترافیک کلاینت‌های IPv6 را دور می‌ریزد. وقتی TUN در حالی شروع شود که API روی یک نشانی شبکه گوش می‌دهد، Throne یک بار هشدار می‌دهد.
- **نمایه‌ای که Throne را از کار می‌اندازد**، اگر در سه شروع پیاپی کمتر از یک دقیقه پس از اجرای دوباره‌اش این کار را کند، دیگر دوباره اجرا نمی‌شود (`resume_suppressed`). آن را با `thronectl connect` اجرا کنید.
- **هسته‌ای که پشت سر هم شکست می‌خورد**، پس از سه شروع پیاپی دیگر باعث خروج Throne با کدهای 70 یا 71 نمی‌شود. Throne با API روشن می‌ماند (`core_gave_up`) و هر 5 دقیقه هسته را دوباره امتحان می‌کند.

## بازیابی دسترسی {#recovery}

اگر تنظیمات ذخیره‌شده با این حال دسترسی شما را قطع کنند، مانند کیل‌سوییچی که نشست SSH شما را قطع می‌کند یا TUN که پاسخ‌های API را به تونل می‌برد، Throne را یک بار با `--no-resume --no-kill-switch` (یا `THRONE_RESUME=0` و `THRONE_KILL_SWITCH=0`) شروع کنید. هیچ نمایه‌ای اجرا نمی‌شود و کیل‌سوییچ خاموش می‌ماند، بنابراین `thronectl` روی همان دستگاه می‌تواند تنظیم را درست کند، مانند `thronectl settings set kill_switch=false` یا `thronectl tun off`. سپس Throne را دوباره به‌طور عادی شروع کنید.

**systemd:**

```bash
printf 'THRONE_RESUME=0\nTHRONE_KILL_SWITCH=0\n' | sudo tee -a /etc/throne/throne.env >/dev/null
sudo systemctl restart throne
thronectl settings set kill_switch=false        # or: thronectl tun off
# remove the two lines from /etc/throne/throne.env again, then:
sudo systemctl restart throne
```

**Docker:** `THRONE_RESUME: "0"` و `THRONE_KILL_SWITCH: "0"` را زیر `environment:` در `compose.yaml` اضافه کنید و `docker compose up -d` را اجرا کنید. تنظیم را با `docker compose exec throne thronectl …` درست کنید، متغیرها را حذف کنید و دوباره `docker compose up -d` را اجرا کنید.

**macOS:** دو متغیر را به `EnvironmentVariables` در فایل plist اضافه کنید و آن را دوباره بارگذاری کنید. تنظیم را درست کنید، متغیرها را حذف کنید و دوباره بارگذاری کنید:

```bash
sudo launchctl bootout system/moe.Throne.headless
sudo launchctl bootstrap system /Library/LaunchDaemons/moe.Throne.headless.plist
```

**سرویس Windows:** آن را با `sc.exe stop Throne` متوقف کنید، سپس Throne را از یک خط فرمان با دسترسی مدیر به‌صورت دستی شروع کنید:

```bat
start "" /wait "C:\Program Files\Throne\Throne.exe" --headless --data-dir C:\ProgramData\Throne --no-resume --no-kill-switch
```

تنظیم را با `thronectl` از یک خط فرمان دیگر با دسترسی مدیر درست کنید ([اجرای فرمان‌ها از خط فرمان](@/headless/installation.fa.md#windows-prompt) را ببینید)، Throne را با `Ctrl+C` متوقف کنید و `sc.exe start Throne` را اجرا کنید.

**شروع از نو:** Throne را متوقف کنید و پوشهٔ داده‌های آن را به جای دیگری منتقل کنید (برای یونیت سیستمی، `/var/lib/throne`). شروع بعدی یک پوشهٔ خالی با یک کلید API جدید می‌سازد. نمایه‌هایتان را از یک [پشتیبان](@/headless/thronectl.fa.md#from-desktop) بازیابی کنید.

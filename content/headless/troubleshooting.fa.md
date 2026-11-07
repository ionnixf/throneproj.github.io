+++
title = "عیب‌یابی"
description = "مشکلات رایج Throne بدون رابط گرافیکی، از یونیتی که شروع نمی‌شود تا تداخل‌های TUN و درخواست‌های ردشدهٔ API، و آنچه باید در گزارش باگ بیاورید."
weight = 60
toc = true
+++

با `Throne --check`، `Throne --health` و آخرین خطوط گزارش شروع کنید ([بررسی سلامت و گزارش‌ها](@/headless/monitoring.fa.md) را ببینید). خط `EXIT <code>: <reason>` می‌گوید چرا Throne خارج شده است؛ [کدهای خروج](@/headless/command_line.fa.md#exit-codes) را ببینید. بیشتر پیام‌های خطا راه رفع خود را می‌گویند. این جدول آن‌هایی را پوشش می‌دهد که نمی‌گویند.

## مشکلات رایج {#symptoms}

| نشانه | راه‌حل |
| --- | --- |
| کد خروج 3: `… is in use by …`، یا برنامهٔ دسکتاپ پیام `Throne is running in the background` را نشان می‌دهد | دو نمونه از Throne از یک پوشهٔ داده‌ها استفاده می‌کنند، مانند برنامهٔ دسکتاپ و یک یونیت کاربری. یکی را متوقف کنید (`systemctl --user stop throne`)، یا Throne بدون رابط گرافیکی را با `thronectl` مدیریت کنید. [اجرا در کنار یک Throne دسکتاپ](@/headless/_index.fa.md#beside-desktop) را ببینید. |
| `--print-connection`: `The API key is not in a file` | تنظیمات کلید خودشان را دارند، همان‌طور که تنظیمات برنامهٔ دسکتاپ دارند. آن کلید را به thronectl بدهید (در برنامهٔ دسکتاپ: `Integration Settings` → `Throne API` → `Copy for scripts`)، یا Throne را با `--api-key-file` اجرا کنید. |
| thronectl: `forbidden` | یک [درخواست مدیریتی](@/headless/thronectl.fa.md#remote) از دستگاه دیگر. Throne را با `--api-admin-lan` اجرا کنید، یا فرمان را روی دستگاه خود Throne اجرا کنید. |
| thronectl: اتصال برقرار نمی‌شود | Throne در حال اجرا نیست، یا `THRONE_ADDR` به جای دیگری اشاره می‌کند. `systemctl status throne` و `Throne --print-connection` را بررسی کنید. |
| `thronectl tun on`: `needs_privileges` | `Throne --check` را اجرا کنید و [دسترسی‌ها در Linux](@/headless/installation.fa.md#linux-privileges) را ببینید. |
| `tun_conflict` | شبکهٔ دیگری از محدودهٔ TUN برنامهٔ Throne استفاده می‌کند: یک محدودهٔ آزاد انتخاب کنید، مانند `thronectl settings set tun_ipv4_cidr=172.29.0.1/24`. Throne یا VPN دیگری TUN را اجرا می‌کند: فقط یکی می‌تواند، پس آن را متوقف کنید. برنامهٔ دیگری `dns_in_port` را در اختیار دارد: پورت دیگری تنظیم کنید. سپس دوباره `thronectl tun on` را اجرا کنید. |
| نمایه‌ها رد می‌شوند: `kill_switch_inactive` | کیل‌سوییچ روشن است اما نمی‌تواند کار کند. `kill_switch_error` در `thronectl --json status` دلیل را می‌گوید: `privilege`، `unsupported`، `config` یا `overridden`. `thronectl settings set kill_switch=false` آن را خاموش می‌کند. [کیل‌سوییچ](@/advanced/kill_switch.fa.md) را ببینید. |
| `degraded: vpn_challenge_pending` یا `vpn_auth_failed` | یک نمایهٔ VPN به ورود به حسابی نیاز دارد که Throne بدون رابط گرافیکی نمی‌تواند انجامش دهد، یا اطلاعات ورود ذخیره‌شدهٔ آن رد شده است. به نمایه یک [حساب OTP](@/advanced/vpn_profiles.fa.md#otp-manager) بدهید، یا اطلاعات ورود را در یک Throne دسکتاپ درست کنید و آن‌ها را با یک [پشتیبان](@/headless/thronectl.fa.md#from-desktop) منتقل کنید. |
| مقدارهای دیگر `degraded:` | [فهرست مقدارها](@/headless/monitoring.fa.md#health) را ببینید. |
| Docker: پورت منتشرشدهٔ 2080 پاسخ نمی‌دهد | ورودی روی `127.0.0.1` گوش می‌دهد. `thronectl settings set inbound_address=0.0.0.0` را اجرا کنید و نمایه را دوباره اجرا کنید. |
| Docker: کانتینر پس از `thronectl shutdown` برمی‌گردد | `restart: unless-stopped` آن را دوباره شروع می‌کند. از `docker compose stop` استفاده کنید. |
| macOS: Throne هر 10 ثانیه شروع می‌شود و خارج می‌شود | launchd یک خطای پیکربندی را دوباره امتحان می‌کند. `/Library/Logs/Throne/throne.log` را بخوانید. |
| Windows: سرویس بی‌درنگ متوقف می‌شود | `sc.exe query Throne` کد خروج Throne را به‌صورت `SERVICE_EXIT_CODE` نشان می‌دهد؛ گزارش در `C:\ProgramData\Throne\config\logs` است. کد 78 اغلب به معنای مشکلی در [پوشهٔ داده‌ها](@/headless/installation.fa.md#windows-data-folder) است. |
| Windows: `tun_failed` همراه با `open existing adapter` | Windows هنوز آداپتور TUN اجرایی را که درست بسته نشده نگه داشته است. `thronectl connect last` را اجرا کنید؛ اگر باز هم شکست خورد، Windows را دوباره راه‌اندازی کنید. |
| کیل‌سوییچ یا TUN دسترسی شما را قطع کرده است | Throne را یک بار با `--no-resume --no-kill-switch` شروع کنید. [بازیابی دسترسی](@/headless/recovery.fa.md#recovery) را ببینید. |

## آنچه باید در گزارش باگ بیاورید {#reporting}

اگر مشکل باقی ماند، [یک باگ گزارش دهید](@/help/bug_reports.fa.md) و این موارد را بیاورید:

- خروجی `Throne --check` و `Throne --health`،
- نحوهٔ اجرای Throne: بسته یا ایمیج، نسخهٔ آن و مدیر سرویس،
- گزارش (log) از شروع تا بروز مشکل، اگر می‌توانید مشکل را بازتولید کنید با `--debug`، و برای کرش `config/logs/crashed-<time>.log` را هم.

پیش از انتشار، نشانی سرورها، URLهای اشتراک و کلیدها را حذف کنید. هرگز `api.key` را منتشر نکنید.

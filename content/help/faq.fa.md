+++
title = "پرسش‌های متداول"
description = "پاسخ‌های کوتاه به پرسش‌های رایج دربارهٔ Throne دسکتاپ."
weight = 10
toc = true
+++

اگر چیزی کار نمی‌کند، [عیب‌یابی](@/help/troubleshooting.fa.md) را ببینید.

## Throne چه تفاوتی با Nekoray دارد؟ {#nekoray}

Throne ادامهٔ Nekoray است که دیگر توسعه داده نمی‌شود. Throne روی هستهٔ خودش، ThroneCore، اجرا می‌شود که بر پایهٔ sing-box ساخته شده و Xray را فقط برای نمایه‌های Xray به کار می‌برد، و قابلیت‌هایی مانند انتخابگر خودکار (Auto Selector)، Cloudflare WARP، نمایه‌های OpenVPN و OpenConnect، پشتیبان‌گیری و یک برنامهٔ Android به آن اضافه شده است. Throne نمی‌تواند پیکربندی Nekoray را بخواند؛ [مهاجرت از Nekoray / NekoBox](@/help/migrating.fa.md) را ببینید.

## گزینه‌ای از منو یا تنظیماتی که در این راهنما آمده، پیدا نمی‌شود. کجاست؟ {#missing-option}

نصب تازه در حالت ساده شروع می‌شود که گزینه‌های پیشرفته را پنهان می‌کند. برای پیدا کردن و باز کردن یک تنظیم، `Ctrl+K` را فشار دهید و نام آن را تایپ کنید، یا در `تنظیمات` (Settings) ← `تنظیمات پایه` (Basic Settings) ← `استایل` (Style) ← `رابط کاربری` (Interface) به حالت پیشرفته بروید. اگر آن گزینه باز هم پیدا نشد، ممکن است نسخهٔ شما قدیمی‌تر از نسخه‌ای باشد که این راهنما توصیف می‌کند، یا آن گزینه فقط در سیستم‌عامل‌های دیگر وجود داشته باشد. [حالت ساده و پیشرفته](@/get_started/configuration.fa.md#interface-mode) را ببینید.

## آیا حالت قابل حمل (پرتابل) وجود دارد؟ {#portable}

بله. بیلدهای ZIP برای Windows و Linux همهٔ داده‌ها را در پوشهٔ `config` در کنار برنامه نگه می‌دارند، بنابراین می‌توانید کل پوشهٔ `Throne` را به پوشه یا رایانهٔ دیگری کپی کنید. بسته‌ها، اسکریپت نصب Linux و برنامهٔ macOS داده‌ها را در پوشهٔ کاربری شما نگه می‌دارند. [خط فرمان، فایل‌ها و گزارش‌ها](@/reference/files.fa.md#data-folder) را ببینید.

## آیا می‌توانم از Throne در Android استفاده کنم و تنظیماتم را به آن منتقل کنم؟ {#android}

بله. [Throne برای Android](@/android/_index.fa.md) از همان هسته و همان قالب پشتیبان‌گیری استفاده می‌کند. با `تنظیمات` (Settings) ← `تنظیمات پایه` (Basic Settings) ← `Backup and Restore` یک پشتیبان بسازید، فایل `.thrbackup` را به گوشی کپی کنید و آن را در برنامه بازیابی کنید؛ این کار در جهت عکس هم انجام می‌شود. [پشتیبان‌گیری، به‌روزرسانی و مهاجرت](@/guides/backup.fa.md#desktop-android) را ببینید.

## آیا Throne می‌تواند بدون پنجره روی سرور اجرا شود؟ {#headless}

بله. `Throne --headless` روی سرور، روتر یا در کانتینر اجرا می‌شود؛ بسته‌های Linux یک سرویس systemd برای آن دارند و یک ایمیج Docker هم وجود دارد. آن را با `thronectl` یا [رابط وب](@/headless/web.fa.md) آن کنترل می‌کنید. [Throne بدون رابط گرافیکی](@/headless/_index.fa.md) را ببینید.

## آیا می‌توانم Throne را از یک اسکریپت یا Stream Deck کنترل کنم؟ {#scripts}

بله. `تنظیمات` (Settings) ← `Integration Settings` → `Throne API` را روشن کنید (فقط در حالت پیشرفته) و از `thronectl` استفاده کنید: برای مثال، `thronectl toggle` روی یک کلید Stream Deck اتصال را برقرار و قطع می‌کند. بدون API هم، [کلیدهای میانبر سراسری](@/reference/shortcuts.fa.md#global-hotkeys) `Start/Stop Profile` و `Toggle Tun Mode` از هر برنامه‌ای کار می‌کنند. [Throne API و thronectl](@/advanced/api.fa.md) را ببینید.

## چرا آنتی‌ویروس من Throne را خطرناک تشخیص می‌دهد؟ {#antivirus}

این‌ها هشدارهای کاذب (false positive) هستند که قابلیت‌های عادی Throne باعثشان می‌شوند: به‌روزرسان فایل‌های برنامه را جایگزین می‌کند، Throne در Windows کلیدهایی در رجیستری می‌نویسد و حالت TUN تنظیمات شبکه را تغییر می‌دهد ([#1127](https://github.com/throneproj/Throne/issues/1127)). Throne را فقط از [صفحهٔ دانلود](@/downloads.fa.md) رسمی دریافت کنید و پوشهٔ Throne را در آنتی‌ویروس به‌عنوان استثنا اضافه کنید. محافظت شبکهٔ آنتی‌ویروس هم ممکن است بدون هیچ پیامی حالت TUN را مسدود کند؛ [عیب‌یابی](@/help/troubleshooting.fa.md#tun) را ببینید.

## آیا شناسهٔ دستگاه من (HWID) برای کسی ارسال می‌شود؟ {#hwid}

فقط اگر خودتان آن را روشن کنید. گزینهٔ `تنظیمات` (Settings) ← `تنظیمات پایه` (Basic Settings) ← `اشتراک` (Subscription) ← `Enable sending HWID, device model, and OS version when updating subscription` به‌طور پیش‌فرض خاموش است؛ وقتی روشن باشد، این مقادیر فقط با درخواست‌های اشتراک فرستاده می‌شوند. هر گروه می‌تواند با `Send HWID` در تنظیمات `Advanced` اشتراک خود، مقدار دیگری برای آن تعیین کند (هر دو فقط در حالت پیشرفته). برخی ارائه‌دهندگان ارسال آن را الزامی کرده‌اند. [حریم خصوصی و درخواست‌های شبکه](@/reference/privacy.fa.md#hwid) را ببینید.

## آیا کیل‌سوییچ (kill switch) وجود دارد؟ {#kill-switch}

بله: `تنظیمات` (Settings) ← `تنظیمات پایه` (Basic Settings) ← `امنیت` (Security) ← `Kill Switch`. تا وقتی Throne در حال اجراست، همهٔ ترافیکی را که از Throne عبور نمی‌کند مسدود می‌کند، حتی وقتی هیچ نمایه‌ای در حال اجرا نیست، و به همان دسترسی‌های حالت TUN نیاز دارد. [کیل‌سوییچ (Kill Switch)](@/advanced/kill_switch.fa.md) را ببینید. در Android، Throne را به‌عنوان Always-on VPN (VPN همیشه روشن) تنظیم کنید و گزینهٔ سیستمی مسدود کردن اتصال‌های بدون VPN را روشن کنید؛ [مجوزها و اجرا در پس‌زمینه](@/android/permissions.fa.md#always-on) را ببینید.

## چرا حالت TUN به دسترسی مدیر یا root نیاز دارد؟ {#tun-admin}

حالت TUN یک آداپتور شبکهٔ مجازی می‌سازد که ترافیک سیستم را می‌گیرد و سیستم‌عامل‌ها این کار را فقط به مدیران اجازه می‌دهند. حالت پروکسی سیستمی به دسترسی خاصی نیاز ندارد. در Windows، Throne با دسترسی مدیر (administrator) دوباره اجرا می‌شود و از آن پس همیشه همین‌طور اجرا می‌شود، مگر اینکه `تنظیمات` (Settings) ← `تنظیمات پایه` (Basic Settings) ← `امنیت` (Security) ← `همیشه به‌عنوان کاربر استاندارد شروع شود` (Always Start as Standard User) را روشن کنید (فقط در حالت پیشرفته). در Linux و macOS فقط هسته دسترسی root می‌گیرد، نه پنجرهٔ Throne. [حالت TUN](@/guides/tun_mode.fa.md#privileges) را ببینید.

## آیا بیت SUID در Linux واقعاً لازم است؟ {#linux-suid}

هسته برای حالت TUN به دسترسی root نیاز دارد، اما پنجرهٔ Throne با کاربر عادی شما اجرا می‌شود؛ پس خود Throne را با `sudo` اجرا نکنید. Throne به‌طور پیش‌فرض با `pkexec` (polkit) مالکیت `ThroneCore` را به root می‌دهد و بیت SUID آن را تنظیم می‌کند. به‌جای آن، می‌توانید با `setcap` پنج capability به هسته بدهید. برای اینکه Throne دیگر دسترسی درخواست نکند، `تنظیمات` (Settings) ← `تنظیمات پایه` (Basic Settings) ← `امنیت` (Security) ← `غیرفعال کردن درخواست دسترسی` (Disable Privilege request) را روشن کنید (فقط در حالت پیشرفته). [حالت TUN](@/guides/tun_mode.fa.md#privileges) را ببینید.

## چرا پس از بسته شدن اجباری Throne اینترنت من کار نمی‌کند؟ {#force-quit}

وقتی `پروکسی سیستمی` (System Proxy) روشن است، Throne تا زمانی که یک نمایه در حال اجراست، تنظیم پروکسی سیستم شما را روی پورت محلی خودش قرار می‌دهد و وقتی نمایه متوقف می‌شود یا از Throne خارج می‌شوید، آن را برمی‌دارد. اگر Throne به‌اجبار بسته شود، این تنظیم باقی می‌ماند و برنامه‌ها سعی می‌کنند از پروکسی‌ای استفاده کنند که دیگر در حال اجرا نیست. برای رفع آن، Throne را اجرا کنید، یک نمایهٔ دلخواه را با `پروکسی سیستمی` تیک‌خورده اجرا کنید، سپس آن را متوقف کنید یا با `برنامه` (Program) ← `خروج` (Exit) خارج شوید. همچنین می‌توانید پروکسی را در تنظیمات شبکهٔ سیستم خود خاموش کنید.

## چرا با توقف نمایه، پروکسی سیستمی خاموش می‌شود؟ {#system-proxy-off}

Throne پروکسی سیستمی را فقط تا زمانی تنظیم می‌کند که یک نمایه در حال اجراست، تا برنامه‌ها هرگز سعی نکنند از پروکسی‌ای استفاده کنند که در حال اجرا نیست. کادر `پروکسی سیستمی` (System Proxy) تیک‌خورده می‌ماند و Throne با اجرای یک نمایه، پروکسی را دوباره تنظیم می‌کند. [پروکسی سیستمی، TUN و اشتراک‌گذاری در شبکهٔ محلی](@/guides/proxy_modes.fa.md#system-proxy) را ببینید.

## چرا تست نشت DNS، سرور DNS ارائه‌دهندهٔ اینترنت من را نشان می‌دهد؟ {#dns-leak-test}

اغلب این رفتار طبیعی است. دامنه‌هایی که با قوانین `direct` شما مطابقت دارند با `دی ان اس مستقیم` (Direct DNS) پرس‌وجو می‌شوند که معمولاً DNS ارائه‌دهندهٔ اینترنت (ISP) شماست، و در حالت پروکسی سیستمی، برنامه‌ها و مرورگرها می‌توانند بدون Throne نام‌ها را پرس‌وجو کنند. [تست‌های نشت DNS](@/guides/dns.fa.md#leak-tests) نتایج مورد انتظار و آنچه باید تغییر دهید را فهرست می‌کند.

## چرا نمایهٔ من پس از به‌روزرسانی اشتراک متوقف شد یا همچنان در حال اجرا ماند؟ {#profile-after-update}

به‌روزرسانی، نمایهٔ در حال اجرا را فقط وقتی متوقف می‌کند که خودتان اجازه دهید. اگر به‌روزرسانی بخواهد نمایهٔ در حال اجرا را حذف کند، Throne آن را نگه می‌دارد، مگر اینکه `تنظیمات` (Settings) ← `تنظیمات پایه` (Basic Settings) ← `اشتراک` (Subscription) ← `Allow stopping the active profile` روشن باشد (فقط در حالت پیشرفته). اگر ارائه‌دهنده فقط تنظیمات آن را تغییر داده باشد، Throne آن‌ها را بدون اتصال دوباره ذخیره می‌کند؛ برای استفاده از آن‌ها، نمایه را دوباره اجرا کنید. [اشتراک‌ها و گروه‌ها](@/guides/subscriptions.fa.md#running-profile) را ببینید.

## چرا اشتراک من نمایه‌های `Custom` نشان می‌دهد؟ {#custom-config-import}

Throne فایل‌های JSON مربوط به sing-box و Xray را به یک نمایه برای هر سرور تقسیم می‌کند، اما پیکربندی‌های کامل Xray (`Custom Xray Config`)، خروجی‌های (outbound) Xray با پروتکلی غیر از VLESS (`Custom Xray … Outbound`) و خروجی‌های تکی sing-box (`Custom … Outbound`) را یکپارچه نگه می‌دارد. خروجی‌های گروهی مانند `selector` و `urltest` وارد نمی‌شوند؛ به‌جای آن از یک [انتخابگر خودکار (Auto Selector)](@/guides/testing.fa.md#auto-selector) استفاده کنید. [پروتکل‌ها و قالب‌های واردکردن](@/reference/protocols.fa.md#import-formats) را ببینید.

## نمایه‌های مسیریابی قابل دانلود از کجا می‌آیند؟ {#route-profiles-source}

از مخزن [throneproj/routeprofiles](https://github.com/throneproj/routeprofiles) می‌آیند که فهرست مجموعه‌قوانین داخلی `geoip-…` و `geosite-…` را هم نگه می‌دارد. فایل‌های مجموعه‌قوانین از پروژه‌هایی مانند MetaCubeX meta-rules-dat، Chocolate4U Iran-sing-box-rules و runetfreedom russia-v2ray-rules-dat می‌آیند. [مسیریابی](@/guides/routing.fa.md#download-profiles) را ببینید.

## بسته‌های `.deb` چه تفاوتی با هم دارند؟ {#deb-variants}

`.deb` معمولی نسخهٔ خودش از Qt را همراه دارد و روی Debian 10، Ubuntu 20.04 یا جدیدتر اجرا می‌شود. `-system-qt.deb` از Qt 6 توزیع شما استفاده می‌کند و به Debian 12، Ubuntu 22.04 یا جدیدتر نیاز دارد. با بستهٔ معمولی شروع کنید و اگر اجرا نشد، برای مثال روی پردازنده‌های بدون SSE4.2 ([#845](https://github.com/throneproj/Throne/issues/845))، از `system-qt` استفاده کنید. همین دو گونه برای arm64 و به‌صورت `.rpm` هم وجود دارد؛ [نصب](@/get_started/installation.fa.md#linux) را ببینید.

## کدام بسته‌ها رسمی هستند؟ {#third-party-packages}

بسته‌های رسمی عبارت‌اند از فایل‌های [صفحهٔ Releases در GitHub](https://github.com/throneproj/Throne/releases)، اسکریپت نصب Linux، مخزن‌های APT و RPM، بستهٔ `throne` در AUR و cask `throneproj/tap/throne` در Homebrew. بسته‌های WinGet، Scoop و Nix، و بسته‌های دیگر AUR مانند `throne-bin`، را جامعهٔ کاربری می‌سازد و توسعه‌دهندگان از آن‌ها پشتیبانی نمی‌کنند ([#1182](https://github.com/throneproj/Throne/issues/1182)، [#1622](https://github.com/throneproj/Throne/issues/1622)). اگر مشکلی فقط با چنین بسته‌ای رخ می‌دهد، آن را به نگه‌دارندهٔ بسته گزارش دهید. [مدیران بسته](@/get_started/installation.fa.md#package-managers) را ببینید.

## برای Windows 7، macOS قدیمی، یک سیستم Linux قدیمی یا پردازندهٔ قدیمی به کدام بیلد نیاز دارم؟ {#old-systems}

- **Windows 7 SP1 و Windows 8:** `windowslegacy64.zip` (64 بیتی)، `windows32.zip` (32 بیتی)، یا نصب‌کنندهٔ universal که خودش بیلد legacy را انتخاب می‌کند.
- **Mac با پردازندهٔ Intel و macOS 10.15 تا 14.3:** `macoslegacy-amd64.zip`. نسخه‌های قدیمی‌تر macOS پشتیبانی نمی‌شوند.
- **توزیع‌های قدیمی‌تر Linux** (Debian 10، Ubuntu 20.04، RHEL 8): فایل ZIP، اسکریپت نصب یا بسته‌های معمولی. بسته‌های `-system-qt` و مخزن‌ها به Debian 12، Ubuntu 22.04، RHEL 9 یا جدیدتر نیاز دارند.
- **پردازنده‌های بدون SSE4.2:** در Windows، `windowslegacy64.zip` و در Linux، یک بستهٔ `-system-qt` ([#845](https://github.com/throneproj/Throne/issues/845)).

جدول صفحهٔ [دانلود](@/downloads.fa.md) حداقل سیستم لازم برای هر فایل را فهرست می‌کند.

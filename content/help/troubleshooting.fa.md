+++
title = "عیب‌یابی"
description = "راه‌حل مشکلات حالت TUN، DNS، اشتراک‌ها، پروکسی سیستمی، کارایی و اجرای Throne دسکتاپ."
weight = 20
toc = true
+++

مشکل خود را پیدا کنید و بررسی‌ها را به ترتیب انجام دهید. تنظیماتی که اینجا نام برده می‌شوند ممکن است در حالت ساده پنهان باشند: `Ctrl+K` را فشار دهید و نام آن را تایپ کنید ([حالت ساده و پیشرفته](@/get_started/configuration.fa.md#interface-mode)). برای Android، [عیب‌یابی Android](@/android/troubleshooting.fa.md) را ببینید. اگر هیچ‌کدام کمک نکرد، [یک باگ گزارش دهید](@/help/bug_reports.fa.md).

## بررسی‌های اولیه {#first-checks}

1. Throne را با `برنامه` (Program) ← `بررسی به‌روزرسانی` (Check For Update) به‌روزرسانی کنید، یا اگر این گزینه خاکستری است، نسخهٔ جدید را از صفحهٔ [دانلود](@/downloads.fa.md) دریافت کنید.
2. زبانهٔ `گزارش‌ها` (Logs) را در پایین پنجرهٔ اصلی بخوانید.
3. نمایه را با `تست نشانی انتخاب‌شده` (Url Test Selected) (`Ctrl+Shift+S`) تست کنید. اگر تست ناموفق بود، ابتدا سرور یا نمایه را درست کنید.
4. اگر نمایه‌ای با `پروکسی سیستمی` (System Proxy) کار می‌کند اما با `حالتvpn` (Tun Mode) کار نمی‌کند، مشکل در راه‌اندازی TUN است.

## حالت TUN {#tun}

### TUN روشن است، اما هیچ چیزی باز نمی‌شود {#tun-no-traffic}

1. برنامه‌های VPN دیگر را ببندید. آن‌ها ممکن است مسیرها (routes) را در اختیار بگیرند.
2. **Windows:** در فایروال Windows به `ThroneCore.exe` اجازه دهید. اگر وقتی Windows پرسید اجازه نداده باشید، Windows هسته را مسدود نگه می‌دارد و دیگر نمی‌پرسد ([#1433](https://github.com/throneproj/Throne/issues/1433)).
3. **Windows:** محافظت شبکهٔ آنتی‌ویروس‌ها، برای مثال Avast یا ESET، ممکن است بدون هیچ پیامی آداپتور TUN را مسدود کند ([#1687](https://github.com/throneproj/Throne/issues/1687)، [#1357](https://github.com/throneproj/Throne/issues/1357)). پوشهٔ Throne را به‌عنوان استثنا اضافه کنید.
4. **Windows:** برای تست، `مسیر سخت‌گیرانه` (Strict Route) را در `تنظیمات vpn` (Tun Settings) خاموش کنید. بدون آن، نشت DNS ممکن است؛ [محافظت از نشت DNS در Windows](@/advanced/windows_tun_mode.fa.md#strict-route) را ببینید.
5. **Linux:** DNS over TLS را برای کل سیستم در systemd-resolved خاموش کنید ([#1146](https://github.com/throneproj/Throne/issues/1146)) و `Auto Redirect` را در `تنظیمات vpn` روشن نگه دارید.

### TUN اجرا نمی‌شود {#tun-start}

- **نبود دسترسی:** درخواست Throne برای دسترسی مدیر (administrator) یا root را بپذیرید ([پرسش‌های متداول](@/help/faq.fa.md#tun-admin)). اگر `غیرفعال کردن درخواست دسترسی` (Disable Privilege request) روشن باشد، باید خودتان دسترسی‌ها را بدهید.
- **«Strict routing unavailable»** (مسیریابی سخت‌گیرانه در دسترس نیست؛ در Windows): `مسیر سخت‌گیرانه` را در `تنظیمات vpn` خاموش کنید و نمایه را دوباره اجرا کنید.
- **«Tun device misbehaving»** (دستگاه Tun درست کار نمی‌کند): در پیام روی `بازنشانی` (Reset)، یا در `تنظیمات vpn` روی `راه‌اندازی مجدد هسته` (Restart Core) کلیک کنید و نمایه را دوباره اجرا کنید. اگر باز هم کار نکرد، رایانه را دوباره راه‌اندازی کنید.
- **اجرا گیر می‌کند** (Windows): یک فایل HOSTS بسیار بزرگ ممکن است باعث شود پاک کردن حافظهٔ نهان DNS گیر کند ([#1906](https://github.com/throneproj/Throne/issues/1906)). آن را کوچک نگه دارید.
- **«حالت Tun روی این رایانه در اختیار یک Throne پس‌زمینه (PID …) است؛ آن را متوقف کنید یا از thronectl استفاده کنید.»** (A background Throne (PID …) owns Tun on this computer; stop it or use thronectl.) [Throne در پس‌زمینه در حال اجراست](#background-throne) را ببینید.

### TUN چیزهای دیگری را از کار می‌اندازد {#tun-side-effects}

هات‌اسپات موبایل، ماشین‌های مجازی، Docker یا VPN محل کار ممکن است تا زمانی که حالت TUN روشن است کار نکنند. [حالت TUN](@/guides/tun_mode.fa.md#side-effects) و [دستورالعمل‌های کاربردی](@/guides/recipes.fa.md) را ببینید.

## DNS {#dns}

### سایت‌ها باز نمی‌شوند یا گزارش‌ها خطاهای DNS را نشان می‌دهند {#dns-errors}

1. در `تنظیمات` (Settings) ← `تنظیمات مسیریابی` (Routing Settings) ← `دی‌ان‌اس` (DNS)، `دی ان اس سمت-سرور` (Remote DNS) باید از طریق پروکسی شما در دسترس باشد و `دی ان اس مستقیم` (Direct DNS) باید بدون آن کار کند. یکی از سرورهای ازپیش‌تعیین‌شده را امتحان کنید.
2. اگر شبکهٔ شما IPv6 ندارد، در `Default Domain Strategy` یا `Resolve Domain Strategy` در زبانهٔ `متداول` (Common) از `ipv6_only` استفاده نکنید ([#1743](https://github.com/throneproj/Throne/issues/1743)). آن‌ها را خالی بگذارید یا از `prefer_ipv4` استفاده کنید.

### تست نشت، DNS ارائه‌دهندهٔ اینترنت من را نشان می‌دهد {#dns-leak}

این معمولاً رفتار طبیعی است؛ [پرسش‌های متداول](@/help/faq.fa.md#dns-leak-test) را ببینید.

## اشتراک‌ها {#subscriptions}

### به‌روزرسانی ناموفق است {#sub-update-fails}

گزارش‌ها پیام `خطای درخواست اشتراک …: …` (`Requesting subscription … error: …`) را همراه با دلیل آن نشان می‌دهند.

- **«درخواست با پروکسی اما هیچ نمایه‌ای شروع نشده است.»** (Request with proxy but no profile started.) وقتی `Use proxy` (`تنظیمات پایه` (Basic Settings) ← `Miscellaneous`) یا `پروکسی سیستمی` روشن باشد، Throne درخواست‌های خودش را از طریق پروکسی خودش می‌فرستد. ابتدا یک نمایه را اجرا کنید، یا هر دو را خاموش کنید. اگر `Disable Mixed Inbound` روشن است، `Use proxy` را خاموش کنید ([#1689](https://github.com/throneproj/Throne/issues/1689)).
- **سایت ارائه‌دهنده در کشور شما مسدود است:** یک نمایهٔ سالم را اجرا کنید، `Use proxy` را روشن کنید و دوباره به‌روزرسانی کنید.
- **سرور پاسخ `403` می‌دهد یا چیز مفیدی نمی‌فرستد:** بسیاری از ارائه‌دهندگان فقط به برنامه‌هایی که می‌شناسند پاسخ می‌دهند. `عامل کاربر` (User Agent) مورد انتظار ارائه‌دهندهٔ خود را برای همهٔ گروه‌ها در `تنظیمات پایه` ← `اشتراک` (Subscription)، یا برای یک گروه در `گروه ها` (Groups) ← `Edit current Group` → `Advanced` تنظیم کنید. مقدار پیش‌فرض `Throne/<version>` است.
- **«Device fingerprint required»** (اثر انگشت دستگاه لازم است) یا پیامی مشابه: ارائه‌دهنده به [HWID](@/help/faq.fa.md#hwid) شما نیاز دارد.

### به‌روزرسانی هیچ نمایه‌ای پیدا نمی‌کند {#sub-no-profiles}

در گزارش‌ها پیام «No profiles found in the subscription» (هیچ نمایه‌ای در اشتراک پیدا نشد) دیده می‌شود. بررسی کنید که لینک یک نشانی اشتراک باشد، نه یک صفحهٔ وب یا لینک یک برنامه: لینک‌های `happ://crypt` و `v2raytun://crypt` برای همان برنامه‌ها رمزنگاری شده‌اند و Throne نمی‌تواند آن‌ها را بخواند. یک `عامل کاربر` (User Agent) دیگر را هم امتحان کنید.

### گروه به‌روز نمی‌شود {#sub-no-update}

- نمایه‌هایی که با `افزودن نمایه‌ها به این گروه` (Add profiles to this group) اضافه شده‌اند نشانی اشتراک ندارند. نشانی را دوباره با `Ctrl+V` جای‌گذاری کنید و `ایجاد گروه اشتراک جدید` (Create new subscription group) را انتخاب کنید.
- به‌روزرسانی خودکار به `تنظیمات پایه` (Basic Settings) ← `اشتراک` (Subscription) ← `Subscription auto update` نیاز دارد که به‌طور پیش‌فرض خاموش است، و گروه‌هایی را که `لغو به‌روزرسانی اتوماتیک` (Skip automatic update) دارند به‌روز نمی‌کند.

نمایه‌ها پس از به‌روزرسانی تغییر کردند یا ناپدید شدند؟ [پرسش‌های متداول](@/help/faq.fa.md#profile-after-update) و [اشتراک‌ها و گروه‌ها](@/guides/subscriptions.fa.md#problems) را ببینید.

## پروکسی سیستمی {#system-proxy}

### برخی برنامه‌ها پروکسی سیستمی را نادیده می‌گیرند {#apps-ignore-proxy}

ترمینال‌هایی مانند PowerShell، برنامه‌های Windows Store، Discord و تماس‌های Telegram معمولاً آن را نادیده می‌گیرند ([#370](https://github.com/throneproj/Throne/issues/370)، [#1631](https://github.com/throneproj/Throne/issues/1631)). برای این برنامه‌ها از `حالتvpn` (Tun Mode) استفاده کنید؛ [از کدام حالت استفاده کنیم](@/guides/proxy_modes.fa.md#which-mode) را ببینید.

### پروکسی سیستمی تنظیم نمی‌شود {#proxy-not-set}

1. یک نمایه را اجرا کنید. Throne پروکسی سیستمی را فقط تا زمانی تنظیم می‌کند که یک نمایه در حال اجراست.
2. **«Cannot set system proxy when mixed inbound is disabled.»** (وقتی ورودی mixed غیرفعال است، نمی‌توان پروکسی سیستمی را تنظیم کرد) `Disable Mixed Inbound` را در `تنظیمات پایه` (Basic Settings) ← `متداول` (Common) خاموش کنید.
3. **Linux:** Throne فقط در GNOME و KDE پروکسی سیستمی را تنظیم می‌کند. در محیط‌های دسکتاپ دیگر، نشانی `Mixed: …` نوشته‌شده در نوار وضعیت را در تنظیمات سیستم خود وارد کنید، یا از حالت TUN استفاده کنید. Throne را با root اجرا نکنید ([#864](https://github.com/throneproj/Throne/issues/864)).

### پس از بسته شدن Throne اینترنت کار نمی‌کند {#no-internet-after-exit}

اگر Throne در حالی که `پروکسی سیستمی` (System Proxy) روشن بود به‌اجبار بسته شده باشد، پروکسی سیستمی همچنان به Throne اشاره می‌کند. [پرسش‌های متداول](@/help/faq.fa.md#force-quit) را ببینید.

## کارایی و کرش‌ها {#performance}

### مصرف بالای CPU یا حافظه {#high-cpu}

1. در مجموع کمتر از حدود 10,000 نمایه نگه دارید. پس از تست URL، نمایه‌های از کار افتاده را با `گروه ها` (Groups) ← `پاکسازی` (Clean up) ← `غیرقابل دستیابی پاک شود` (Remove Unavailable) حذف کنید.
2. کلاینت‌های تورنت با روشن بودن Multiplex ممکن است CPU زیادی مصرف کنند ([#1090](https://github.com/throneproj/Throne/issues/1090)). Multiplex به‌طور پیش‌فرض خاموش است؛ اگر آن را روشن کرده‌اید، در نمایه خاموشش کنید، یا `Default On` را در `تنظیمات` (Settings) ← `Preset Settings` → `Multiplex` خاموش کنید.
3. تیک `تنظیمات پایه` (Basic Settings) ← `استایل` (Style) ← `آمار اتصال` (Connection statistics) ← `فعال کردن` (Enable) را بردارید. در این صورت زبانهٔ `اتصال‌ها` (Connections) خالی می‌ماند.
4. **Windows:** `فعال کردن مسیریابی Tun` (Enable Tun Routing) همراه با مجموعه‌قوانین بزرگ ممکن است مصرف CPU را بسیار بالا ببرد. آن را در `تنظیمات vpn` (Tun Settings) خاموش کنید.

### Throne یا هسته کرش می‌کند {#crashes}

- **«هسته بیش از حد متوقف می‌شود، راه‌اندازی خودکار این نمایه متوقف شود.»** (Core exits too frequently, stop automatic restart this profile.) هسته پشت سر هم کرش می‌کند، پس Throne دیگر آن را همراه با نمایهٔ شما دوباره اجرا نمی‌کند.
- **«Throne did not shut down cleanly last time»** (Throne دفعهٔ قبل به‌درستی بسته نشد) در اجرای بعدی: Throne کرش کرده است. گزارش آن جلسه به‌صورت `logs/crashed-<date>-<time>.log` در پوشهٔ پیکربندی نگه داشته می‌شود؛ در Windows، یک گزارش کرش هم در پوشهٔ `crashes` ذخیره می‌شود.
- برای کنار گذاشتن احتمال مشکل در پیکربندی، از Throne خارج شوید، نام پوشهٔ `config` را تغییر دهید و Throne را دوباره اجرا کنید؛ [خط فرمان، فایل‌ها و گزارش‌ها](@/reference/files.fa.md#reset) را ببینید.
- کرش را گزارش دهید و این فایل‌ها را پیوست کنید: [گزارش باگ](@/help/bug_reports.fa.md#logs).

## مشکلات اجرا و محیط دسکتاپ {#startup}

### Throne اجرا نمی‌شود {#no-start}

- **«Incompatible processor. This Qt build requires the following features: sse4.2 popcnt»** (پردازنده ناسازگار است): در Windows از `windowslegacy64.zip` و در Linux از یک بستهٔ `-system-qt` استفاده کنید ([پرسش‌های متداول](@/help/faq.fa.md#old-systems)).
- **Linux: `Could not load the Qt platform plugin "xcb"`**: اگر Throne از Qt سیستم شما استفاده می‌کند، `libxcb-cursor0` (Debian، Ubuntu) یا `xcb-util-cursor` (Fedora، RHEL) را نصب کنید ([#1096](https://github.com/throneproj/Throne/issues/1096)). اگر فایل ZIP یا یک بستهٔ معمولی این خطا را نشان می‌دهد، از یک بستهٔ `-system-qt` استفاده کنید؛ این بسته برای خطاهای دیگر Qt هم کمک می‌کند.
- **macOS: «Throne is damaged and can't be opened»** (Throne آسیب دیده است و باز نمی‌شود): Throne با گواهی Apple امضا نشده است. `Throne.app` را به `/Applications` منتقل کنید، پرچم قرنطینه را در Terminal حذف کنید و Throne را دوباره باز کنید:

  ```bash
  xattr -d com.apple.quarantine /Applications/Throne.app
  ```

در Linux، Throne را از ترمینال اجرا کنید تا خطا را ببینید: `/opt/Throne/Throne -appdata` برای بسته‌ها، یا `./Throne` در پوشهٔ نسخهٔ ZIP. از `sudo` استفاده نکنید؛ فقط هسته به دسترسی root نیاز دارد ([پرسش‌های متداول](@/help/faq.fa.md#linux-suid)).

### آیکون سینی سیستم در GNOME دیده نمی‌شود {#gnome-tray}

یک افزونهٔ AppIndicator نصب و فعال کنید. اگر پنجره پنهان است، Throne را دوباره اجرا کنید تا پنجره نشان داده شود، یا `تنظیمات پایه` (Basic Settings) ← `استایل` (Style) ← `غیرفعال‌سازی سینی` (Disable tray) را روشن کنید تا بستن پنجره، Throne را ببندد.

### Throne پس از راه‌اندازی دوباره وصل نمی‌شود {#reconnect}

`برنامه` (Program) ← `آخرین نمایه را به خاطر بسپار` (Remember last profile) را روشن کنید. در اجرای بعدی، Throne نمایه‌ای را که هنگام خروج در حال اجرا بود دوباره اجرا می‌کند و `پروکسی سیستمی` (System Proxy) و `حالتvpn` (Tun Mode) را به همان حالت قبل برمی‌گرداند. `برنامه` ← `با سیستم شروع شود` (Start with system) Throne را هنگام ورود شما به سیستم اجرا می‌کند؛ در Windows، اگر Throne با دسترسی مدیر اجرا می‌شود، این گزینه آن دسترسی را هم بدون پرسیدن می‌گیرد ([پرسش‌های متداول](@/help/faq.fa.md#tun-admin)).

### لینک‌های `throne://` کاری انجام نمی‌دهند {#deeplinks}

در Windows و Linux، نسخه‌های ZIP به‌طور پیش‌فرض لینک‌های `throne://` را ثبت نمی‌کنند. `تنظیمات پایه` (Basic Settings) ← `متداول` (Common) ← `Register throne:// links at startup` را روشن کنید، یا روی `Install` در کنار آن کلیک کنید. بررسی‌های بیشتر: [لینک‌های عمیق](@/advanced/deeplinks.fa.md#troubleshooting).

### Throne در پس‌زمینه در حال اجراست {#background-throne}

یک [Throne بدون رابط گرافیکی](@/headless/_index.fa.md)، برای مثال یک سرویس systemd، می‌تواند روی همان رایانه‌ای اجرا شود که پنجرهٔ Throne روی آن است:

- **«Throne is running in the background (PID …). Stop the service or use thronectl.»** (Throne در پس‌زمینه در حال اجراست (PID …). سرویس را متوقف کنید یا از thronectl استفاده کنید.) و پنجره باز نمی‌شود: Throne بدون رابط گرافیکی از همان پوشهٔ داده استفاده می‌کند. آن را متوقف کنید (برای مثال با `systemctl --user stop throne`) و Throne را دوباره اجرا کنید، یا آن را با [thronectl](@/advanced/api.fa.md) مدیریت کنید.
- **«یک Throne پس‌زمینه (PID …) روی این رایانه در حال اجراست»** (A background Throne (PID …) is running on this computer): یک Throne بدون رابط گرافیکی با پوشهٔ دادهٔ دیگری اجرا می‌شود. پنجرهٔ Throne کار می‌کند، اما بدون حالت TUN.
- **«حالت Tun روی این رایانه در اختیار یک Throne پس‌زمینه (PID …) است؛ آن را متوقف کنید یا از thronectl استفاده کنید.»** (A background Throne (PID …) owns Tun on this computer; stop it or use thronectl.) تا وقتی هر Throne بدون رابط گرافیکی‌ای اجرا می‌شود، پنجره نمی‌تواند از حالت TUN استفاده کند، حتی اگر آن Throne خودش از TUN استفاده نکند. Throne بدون رابط گرافیکی را متوقف کنید.
- **«تا زمانی که یک Throne پس‌زمینه (PID …) از همین نصب اجرا می‌شود، به‌روزرسانی در دسترس نیست؛ ابتدا آن را متوقف کنید.»** (Update is unavailable while a background Throne (PID …) runs from this installation; stop it first.) آن را متوقف کنید، به‌روزرسانی کنید و دوباره اجرایش کنید.
- **«Another Throne instance (PID …) is using this data directory»** (نمونهٔ دیگری از Throne (PID …) از این پوشهٔ داده استفاده می‌کند): Throne دیگری پوشهٔ داده را در اختیار دارد اما پاسخ نمی‌دهد. از آن خارج شوید، یا پردازهٔ آن را ببندید، و Throne را دوباره اجرا کنید.

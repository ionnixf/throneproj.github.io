+++
title = "Cloudflare WARP"
description = "Cloudflare WARP را به‌عنوان یک خروجی اضافه پشت پروکسی خود قرار دهید و بگذارید ترافیک انتخاب‌شده بدون آن عبور کند."
weight = 40
toc = true
+++

WARP سرویس VPN رایگان Cloudflare است. Throne می‌تواند آن را به‌عنوان یک هاپ اضافه پس از پروکسی شما قرار دهد: دستگاه شما ← نمایه‌ای که اجرا می‌کنید ← WARP ← اینترنت. در این صورت وب‌سایت‌ها به‌جای نشانی سرور پروکسی شما یک نشانی Cloudflare می‌بینند؛ این کار برای سایت‌هایی مفید است که نشانی‌های VPN یا مراکز داده (data center) را مسدود می‌کنند.

- WARP روی هر نمایه‌ای که اجرا می‌کنید اعمال می‌شود، از جمله زنجیره‌ها و انتخابگرهای خودکار (auto selector)، و پس از پروکسی مقصد (landing proxy) گروه قرار می‌گیرد. فقط نمایه‌های `سفارشی (sing-box پیکربندی)` (Custom (sing-box config)) بدون آن اجرا می‌شوند.
- WARP از راه پروکسی شما اجرا می‌شود؛ بنابراین نمایه باید ترافیک آن را حمل کند: حالت‌های `WireGuard` و `AmneziaWG` به UDP نیاز دارند و `MASQUE` روی HTTP/3 هم همین‌طور. `MASQUE` روی HTTP/2 فقط به TCP نیاز دارد.

## ساختن پیکربندی WARP {#generate}

1. `مسیریابی` (Routing) ← `تنظیمات مسیریابی` (Routing Settings) ← زبانهٔ `Warp` را باز کنید (در حالت ساده، بخش `Warp` در زبانهٔ `متداول` (Common)).
2. `Mode` را انتخاب کنید: `WireGuard`، `MASQUE` یا `AmneziaWG`.
3. روی `Generate Warp Config` کلیک کنید و بار اول شرایط استفاده از خدمات Cloudflare WARP را بپذیرید.
4. وقتی دکمه «Success!» (موفق شد!) را نشان داد، روی `تایید` (OK) کلیک کنید.

`WireGuard` و `MASQUE` پیکربندی‌های جداگانه دارند؛ پس اگر میان آن‌ها جابه‌جا شدید، دوباره پیکربندی بسازید. `AmneziaWG` از پیکربندی `WireGuard` استفاده می‌کند.

در `MASQUE`، مقدار پیش‌فرض `HTTP Version` از HTTP/3 روی UDP استفاده می‌کند؛ `HTTP/2` روی TCP اجرا می‌شود و جایی که UDP مسدود است کار می‌کند. فیلدهای ساخته‌شده، `HTTP Version` و `Registration Domains…` فقط در حالت پیشرفته هستند ([حالت ساده و پیشرفته](@/get_started/configuration.fa.md#interface-mode) را ببینید).

### حالت AmneziaWG {#amneziawg}

وقتی از `AmneziaWG` استفاده کنید که حالت `WireGuard` وصل نمی‌شود، چون WireGuard به Cloudflare مسدود است. این حالت پیش از handshake بسته‌هایی می‌فرستد که شبیه یک اتصال HTTP/3 (QUIC) به `www.cloudflare.com` هستند (امضا یا signature)، همراه با چند بستهٔ بی‌مصرف (junk) که WARP آن‌ها را می‌پذیرد. بسته‌های WARP از سرور پروکسی شما بیرون می‌روند، پس آنچه اهمیت دارد شبکهٔ سرور است، نه شبکهٔ شما.

Throne وقتی `AmneziaWG` را انتخاب می‌کنید امضا را می‌سازد. پیکربندی WireGuard که پیش‌تر ساخته‌اید همچنان کار می‌کند.

### اگر ثبت ناموفق بود {#registration-fails}

سرور ثبت Cloudflare، یعنی `api.cloudflareclient.com`، در برخی کشورها مسدود است ([#1874](https://github.com/throneproj/Throne/issues/1874)). از راه پروکسی خود ثبت کنید:

1. یک نمایهٔ سالم را اجرا کنید.
2. `Use proxy` را در `تنظیمات` (Settings) ← `تنظیمات پایه` (Basic Settings) ← `Miscellaneous` تیک بزنید (در حالت ساده: `Network Settings` در زبانهٔ `متداول` (Common)). وقتی `پروکسی سیستمی` (System Proxy) روشن است، Throne به‌هرحال همین کار را می‌کند.
3. دوباره روی `Generate Warp Config` کلیک کنید.

- «درخواست با پروکسی اما هیچ نمایه‌ای شروع نشده است.» (Request with proxy but no profile started.): ابتدا یک نمایه را اجرا کنید.
- وقتی `Disable Mixed Inbound` روشن است، درخواست به حالت TUN هم نیاز دارد.
- وقتی [کیل‌سوییچ](@/advanced/kill_switch.fa.md) روشن است، پیکربندی را در حالی بسازید که نمایه‌ای در حالت TUN اجرا می‌شود، یا وقتی `Use proxy` روشن است.

`Registration Domains…` دامنه‌های API مربوط به Cloudflare را فهرست می‌کند که به ترتیب امتحان می‌شوند، هر کدام در یک خط. فهرست خالی یعنی `api.cloudflareclient.com`.

## روشن کردن WARP {#enable}

روی `مسیریابی` (Routing) ← `Enable Warp` کلیک کنید (برای خاموش کردن، دوباره کلیک کنید). نمایهٔ در حال اجرا با تنظیم جدید دوباره راه‌اندازی می‌شود.

اگر حالت فعلی پیکربندی نداشته باشد، یا `AmneziaWG` امضا نداشته باشد، نمایه اجرا نمی‌شود: ابتدا پیکربندی را بسازید. برای اطمینان از کارکرد WARP، وب‌سایتی را باز کنید که نشانی IP شما را نشان می‌دهد: باید یک نشانی Cloudflare نمایش داده شود.

## عبور بخشی از ترافیک بدون WARP {#warp-bypass}

`warp-bypass` خروجی‌ای است که از نمایهٔ شما بدون هاپ WARP استفاده می‌کند. آن را در ویرایشگر نمایهٔ مسیریابی تنظیم کنید (`تنظیمات مسیریابی` (Routing Settings) ← زبانهٔ `مسیر` (Route) ← یک نمایه را انتخاب کنید ← `Edit`):

| هدف | `Default outbound` | سایت‌ها را کجا بگذارید |
| --- | --- | --- |
| بیشتر ترافیک از راه WARP، برخی سایت‌ها بدون آن | `proxy` | کادر `Warp-bypass` |
| بیشتر ترافیک بدون WARP، برخی سایت‌ها از راه آن | `warp-bypass` | کادر `پروکسی` (Proxy) |

قوانین پیشرفته هم می‌توانند از `warp-bypass` به‌عنوان خروجی استفاده کنند ([مسیریابی](@/guides/routing.fa.md#simple-rules) را ببینید). وقتی WARP خاموش است، `warp-bypass` مانند `proxy` کار می‌کند.

## WARP به‌عنوان یک نمایهٔ معمولی {#warp-profiles}

برای استفاده از WARP بدون پروکسی دیگر، یا به‌عنوان یک هاپ در زنجیره، آن را به یک نمایهٔ مستقل تبدیل کنید:

1. `برنامه` (Program) ← `نمایه جدید` (New profile) را باز کنید و `نوع` (Type) را روی `WireGuard` یا `MASQUE` بگذارید.
2. روی `Generate Warp Config` (برای WireGuard) یا `Generate WARP identity` (برای MASQUE) کلیک کنید.
3. یک `اسم` (Name) وارد کنید و روی `تایید` (OK) کلیک کنید.

چنین نمایه‌ای مستقیماً به Cloudflare وصل می‌شود. تا وقتی از آن استفاده می‌کنید، `Enable Warp` را خاموش نگه دارید؛ وگرنه Throne یک هاپ WARP دوم پس از آن اضافه می‌کند.

`Mode` در `تنظیمات مسیریابی` (Routing Settings) روی آن اثری ندارد. برای پنهان‌سازی حالت `AmneziaWG` در یک نمایهٔ WireGuard، `Enable Amnezia` را تیک بزنید، روی `Amnezia Options` کلیک کنید و `Junk Packet Count (Jc)` را روی 4، `Junk Packet Min Size (Jmin)` را روی 40، `Junk Packet Max Size (Jmax)` را روی 70 و H1 تا H4 را روی 1، 2، 3 و 4 بگذارید. سپس روی `Generate QUIC Signature...` کلیک کنید، `www.cloudflare.com` را وارد کنید و روی `تایید` (OK) کلیک کنید.

## Android {#android}

- تنظیمات در `تنظیمات` (Settings) ← `مسیریابی` (Routing) ← `WARP` قرار دارند و مانند نسخهٔ دسکتاپ کار می‌کنند، از جمله `AmneziaWG`.
- اگر ثبت مسدود است، ابتدا وصل شوید و `تنظیمات` (Settings) ← `اشتراک‌ها` (Subscriptions) ← `استفاده از پراکسی` (Use proxy) را روشن کنید.
- برای روشن و خاموش کردن سریع WARP، `تنظیمات` (Settings) ← `مسیریابی` (Routing) ← `پروفایل مسیریابی فعلی` (Current routing profile) را باز کنید و روی `فعال‌سازی WARP` (Enable WARP) یا `غیرفعال‌سازی WARP` (Disable WARP) بزنید.
- ویرایشگرهای نمایهٔ WireGuard و MASQUE یک دکمهٔ `ایجاد هویت WARP` (Generate WARP identity) دارند.

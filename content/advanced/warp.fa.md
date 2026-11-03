+++
title = "Cloudflare WARP"
description = "Cloudflare WARP را به‌عنوان یک خروجی اضافه پشت پروکسی خود قرار دهید، پیکربندی آن را بسازید، روشنش کنید و بگذارید ترافیک انتخاب‌شده بدون آن عبور کند."
weight = 40
toc = true
+++

WARP سرویس VPN رایگان Cloudflare است. Throne می‌تواند WARP را به‌عنوان یک هاپ اضافه پس از پروکسی شما قرار دهد. در این صورت وب‌سایت‌ها به‌جای نشانی سرور پروکسی شما، یک نشانی Cloudflare می‌بینند. این کار برای سایت‌هایی مفید است که نشانی‌های VPN یا مراکز داده (data center) را مسدود می‌کنند.

## WARP در Throne چگونه کار می‌کند {#how-it-works}

وقتی WARP روشن است، مسیر ترافیک این است: دستگاه شما ← نمایه‌ای که اجرا می‌کنید ← WARP ← اینترنت.

- WARP روی هر نمایه‌ای که اجرا می‌کنید اعمال می‌شود، از جمله زنجیره‌ها و انتخابگرهای خودکار (auto selector). فقط نمایه‌های `سفارشی (sing-box پیکربندی)` (Custom (sing-box config)) بدون آن اجرا می‌شوند. اگر گروه پروکسی مقصد (landing proxy) داشته باشد، WARP پس از آن قرار می‌گیرد.
- WARP از راه پروکسی شما اجرا می‌شود؛ بنابراین پروکسی باید ترافیک WARP را حمل کند:
  - حالت‌های `WireGuard` و `AmneziaWG` از UDP استفاده می‌کنند. نمایه باید UDP را عبور دهد.
  - حالت `MASQUE` روی HTTP/3 هم به UDP نیاز دارد. MASQUE روی HTTP/2 فقط به TCP نیاز دارد.
- قوانین و خروجی پیش‌فرض می‌توانند ترافیک را بدون WARP از پروکسی شما عبور دهند. [عبور بخشی از ترافیک بدون WARP](#warp-bypass) را ببینید.

راه‌اندازی WARP دو گام دارد: یک بار پیکربندی را بسازید، سپس WARP را روشن کنید.

## ساختن پیکربندی WARP {#generate}

1. `مسیریابی` (Routing) ← `تنظیمات مسیریابی` (Routing Settings) را باز کنید.
2. زبانهٔ `Warp` را باز کنید.
3. `Mode` را انتخاب کنید: `WireGuard`، `MASQUE` یا `AmneziaWG`.
4. روی `Generate Warp Config` کلیک کنید.
5. بار اول، Throne می‌پرسد که آیا شرایط استفاده از خدمات Cloudflare WARP را می‌پذیرید. روی `بله` (Yes) کلیک کنید.
6. صبر کنید تا دکمه «Success!» (موفق شد!) را نشان دهد. اکنون فیلدهای حالت انتخاب‌شده پر شده‌اند.
7. روی `تایید` (OK) کلیک کنید.

در حالت ساده، `Warp` بخشی از زبانهٔ `متداول` (Common) است که فقط `Enable Warp`، `Mode` و `Generate Warp Config` را نشان می‌دهد. فیلدهای زیر و `Registration Domains…` پنهان هستند (فقط در حالت پیشرفته؛ [حالت ساده و پیشرفته](@/get_started/configuration.fa.md#interface-mode) را ببینید).

`WireGuard` و `MASQUE` هر کدام فیلدهای خودشان را دارند. اگر بعداً میان آن‌ها جابه‌جا شدید، برای حالت دیگر هم پیکربندی بسازید. `AmneziaWG` از فیلدهای `WireGuard` استفاده می‌کند؛ [حالت AmneziaWG](#amneziawg) را ببینید.

| حالت | فیلدها |
| --- | --- |
| `WireGuard`، `AmneziaWG` | `Endpoint`، `Private Key`، `Public Key`، `Interface Addresses`، `Reserved` |
| `MASQUE` | `Endpoint`، `Private Key`، `Peer Public Key`، `Interface Addresses`، `SNI`، `HTTP Version` |

`HTTP Version` در MASQUE می‌تواند `HTTP/3 (fallback to HTTP/2)` (پیش‌فرض)، `HTTP/3 only` یا `HTTP/2` باشد. HTTP/3 روی UDP اجرا می‌شود. HTTP/2 روی TLS بر بستر TCP اجرا می‌شود و جایی که UDP مسدود است کار می‌کند.

هر بار ساختن پیکربندی، یک دستگاه جدید در Cloudflare ثبت می‌کند.

### حالت AmneziaWG {#amneziawg}

از نسخهٔ 1.4.0، حالت `AmneziaWG` شناسایی اتصال WireGuard مربوط به WARP را سخت‌تر می‌کند. این حالت پیش از handshake مربوط به WireGuard بسته‌های اضافه‌ای می‌فرستد: چند بسته که شبیه آغاز یک اتصال HTTP/3 (QUIC) به `www.cloudflare.com` هستند، و چهار بستهٔ تصادفی بی‌مصرف (junk) با اندازهٔ 40 تا 70 بایت. این بسته‌ها با WARP سازگارند، پس اتصال مانند حالت `WireGuard` کار می‌کند. وقتی از این حالت استفاده کنید که حالت `WireGuard` وصل نمی‌شود، چون WireGuard به Cloudflare مسدود است. بسته‌های WARP از سرور پروکسی شما بیرون می‌روند، پس آنچه اهمیت دارد شبکهٔ سرور است، نه شبکهٔ شما.

به بسته‌های QUIC امضا (signature) گفته می‌شود. Throne آن‌ها را به‌صورت آفلاین، روی رایانهٔ شما، می‌سازد و همراه با تنظیمات ذخیره می‌کند:

- وقتی `AmneziaWG` را در `Mode` انتخاب می‌کنید و هنوز امضایی وجود ندارد؛
- هر بار که در این حالت روی `Generate Warp Config` کلیک می‌کنید، همراه با یک پیکربندی WireGuard جدید.

پیکربندی WireGuard که پیش‌تر ساخته‌اید همچنان کار می‌کند، پس وقتی از `WireGuard` به `AmneziaWG` می‌روید لازم نیست دوباره پیکربندی بسازید. اگر امضا وجود نداشته باشد و `Enable Warp` تیک خورده باشد، `تنظیمات مسیریابی` (Routing Settings) پیام «AmneziaWG has no QUIC signature yet: click Generate Warp Config.» (AmneziaWG هنوز امضای QUIC ندارد: روی Generate Warp Config کلیک کنید.) یا خطایی را که جلوی ساختن آن را گرفته است نشان می‌دهد.

### اگر ثبت ناموفق بود {#registration-fails}

سرور ثبت Cloudflare، یعنی `api.cloudflareclient.com`، در برخی کشورها مسدود است ([#1874](https://github.com/throneproj/Throne/issues/1874)). در این صورت، از راه پروکسی خود ثبت کنید:

1. یک نمایهٔ سالم را اجرا کنید.
2. `تنظیمات` (Settings) ← `تنظیمات پایه` (Basic Settings) ← `Miscellaneous` را باز کنید و `Use proxy` را تیک بزنید. در حالت ساده، `Use proxy` در بخش `Network Settings` از زبانهٔ `متداول` (Common) قرار دارد. وقتی `پروکسی سیستمی` (System Proxy) روشن است، Throne به‌هرحال درخواست‌های خودش را از راه پروکسی می‌فرستد.
3. دوباره روی `Generate Warp Config` کلیک کنید.

اگر هنگامی که `Use proxy` یا `پروکسی سیستمی` (System Proxy) روشن است هیچ نمایه‌ای در حال اجرا نباشد، ساختن پیکربندی با پیام «درخواست با پروکسی اما هیچ نمایه‌ای شروع نشده است.» (Request with proxy but no profile started.) ناموفق می‌شود.

Throne چنین درخواست‌هایی را به درگاه پروکسی محلی خود می‌فرستد. از نسخهٔ 1.4.0، وقتی `Disable Mixed Inbound` روشن است، Throne آن‌ها را مستقیم می‌فرستد و حالت TUN آن‌ها را از راه نمایهٔ در حال اجرا عبور می‌دهد. اگر حالت TUN هم خاموش باشد، ساختن پیکربندی با پیام «Request with proxy but the mixed inbound is disabled and Tun Mode is off.» (درخواست با پروکسی، اما ورودی mixed غیرفعال و حالت TUN خاموش است) ناموفق می‌شود.

وقتی [کیل‌سوییچ](@/advanced/kill_switch.fa.md) روشن است، درخواست‌های خود Throne مسدود می‌شوند، مگر اینکه از Throne عبور کنند. پیکربندی را در حالی بسازید که نمایه‌ای در حالت TUN اجرا می‌شود، یا وقتی `Use proxy` روشن است.

`Registration Domains…` دامنه‌های API مربوط به Cloudflare را، هر کدام در یک خط، نگه می‌دارد. Throne آن‌ها را به ترتیب امتحان می‌کند و از نخستین دامنه‌ای که ثبت را بپذیرد استفاده می‌کند. فهرست خالی یعنی `api.cloudflareclient.com`.

## روشن کردن WARP {#enable}

روی `مسیریابی` (Routing) ← `Enable Warp` کلیک کنید. علامت تیک نشان می‌دهد که WARP روشن است. برای خاموش کردن WARP دوباره روی آن کلیک کنید. نمایهٔ در حال اجرا با تنظیم جدید دوباره راه‌اندازی می‌شود.

همچنین می‌توانید `Enable Warp` را در زبانهٔ `Warp` تیک بزنید و روی `تایید` (OK) کلیک کنید. سپس برای اعمال تغییر، نمایه را دوباره اجرا کنید.

اگر WARP روشن باشد اما برای حالت فعلی پیکربندی ساخته نشده باشد (در حالت `AmneziaWG`، همچنین وقتی امضا وجود ندارد)، نمایه اجرا نمی‌شود و Throne این پیام را نشان می‌دهد: «Warp is enabled but its config has not been generated. Please generate the Warp config first in Routing Settings.» (WARP روشن است اما پیکربندی آن ساخته نشده است؛ ابتدا پیکربندی WARP را در تنظیمات مسیریابی بسازید.) در حالت ساده، وقتی `Enable Warp` تیک خورده باشد و حالت فعلی پیکربندی نداشته باشد، `تنظیمات مسیریابی` (Routing Settings) هم پیام «WARP هنوز پیکربندی ندارد: روی «Generate Warp Config» کلیک کنید.» (WARP has no config yet: click Generate Warp Config.) را نشان می‌دهد.

برای اطمینان از کارکرد WARP، وب‌سایتی را باز کنید که نشانی IP شما را نشان می‌دهد. باید یک نشانی Cloudflare نمایش داده شود.

## عبور بخشی از ترافیک بدون WARP {#warp-bypass}

`warp-bypass` خروجی‌ای است که از نمایهٔ شما بدون هاپ WARP استفاده می‌کند. از آن در یک نمایهٔ مسیریابی استفاده کنید:

| هدف | `Default outbound` | قوانین |
| --- | --- | --- |
| بیشتر ترافیک از راه WARP، برخی سایت‌ها بدون آن | `proxy` | آن سایت‌ها را در کادر `Warp-bypass` قرار دهید. |
| بیشتر ترافیک بدون WARP، برخی سایت‌ها از راه آن | `warp-bypass` | آن سایت‌ها را در کادر `پروکسی` (Proxy) قرار دهید. |

فهرست `Default outbound` و کادر `Warp-bypass` در ویرایشگر نمایهٔ مسیریابی قرار دارند: `تنظیمات مسیریابی` (Routing Settings) ← زبانهٔ `مسیر` (Route) ← یک نمایه را انتخاب کنید ← `Edit`. قوانین پیشرفته هم می‌توانند از `warp-bypass` به‌عنوان خروجی استفاده کنند. برای قالب قوانین، [مسیریابی](@/guides/routing.fa.md#simple-rules) را ببینید.

وقتی WARP خاموش است، `warp-bypass` درست مانند `proxy` کار می‌کند.

## WARP به‌عنوان یک نمایهٔ معمولی {#warp-profiles}

همچنین می‌توانید WARP را به یک نمایهٔ مستقل تبدیل کنید، برای مثال برای استفاده از آن بدون پروکسی دیگر، یا به‌عنوان یک هاپ در زنجیره.

1. `برنامه` (Program) ← `نمایه جدید` (New profile) را باز کنید.
2. `نوع` (Type) را روی `WireGuard` یا `MASQUE` بگذارید.
3. روی `Generate Warp Config` (برای WireGuard) یا `Generate WARP identity` (برای MASQUE) کلیک کنید. Throne کلیدها، نشانی‌ها، MTU و نشانی سرور را پر می‌کند.
4. یک `اسم` (Name) وارد کنید و روی `تایید` (OK) کلیک کنید.

`Mode` در `تنظیمات مسیریابی` (Routing Settings) چنین نمایه‌ای را تغییر نمی‌دهد. برای اینکه یک نمایهٔ WARP از نوع WireGuard همان پنهان‌سازی حالت `AmneziaWG` را داشته باشد، `Enable Amnezia` را تیک بزنید، روی `Amnezia Options` کلیک کنید و `Junk Packet Count (Jc)` را روی 4، `Junk Packet Min Size (Jmin)` را روی 40، `Junk Packet Max Size (Jmax)` را روی 70 و چهار سرآیند جادویی H1 تا H4 را روی 1، 2، 3 و 4 بگذارید. سپس روی `Generate QUIC Signature...` کلیک کنید، `www.cloudflare.com` را وارد کنید و روی `تایید` (OK) کلیک کنید.

چنین نمایه‌ای به‌تنهایی مستقیماً به Cloudflare وصل می‌شود. تا وقتی از آن استفاده می‌کنید، `Enable Warp` را خاموش نگه دارید؛ وگرنه Throne یک هاپ WARP دوم پس از آن اضافه می‌کند.

## Android {#android}

- تنظیمات در `تنظیمات` (Settings) ← `مسیریابی` (Routing) ← `WARP` قرار دارند: `فعال‌سازی WARP` (Enable WARP)، `حالت` (Mode)، فیلدهای هر حالت، `ایجاد پیکربندی` (Generate WARP config) و `دامنه‌های ثبت` (Registration domains).
- نخستین ساختن پیکربندی از شما می‌خواهد شرایط استفاده از خدمات Cloudflare WARP را بپذیرید.
- از نسخهٔ 2.0.5، `حالت` (Mode) گزینهٔ `AmneziaWG` را هم دارد که مانند نسخهٔ دسکتاپ کار می‌کند. برنامه وقتی این حالت را انتخاب می‌کنید امضا را می‌سازد و `ایجاد پیکربندی` (Generate WARP config) یک امضای جدید می‌سازد. تا وقتی امضا وجود ندارد، ردیف `حالت` پیام «AmneziaWG هنوز امضای QUIC ندارد: روی «ایجاد پیکربندی» بزنید.» (AmneziaWG has no QUIC signature yet: tap Generate WARP config.) را نشان می‌دهد.
- اگر ثبت مسدود است، ابتدا وصل شوید و `تنظیمات` (Settings) ← `اشتراک‌ها` (Subscriptions) ← `استفاده از پراکسی` (Use proxy) را روشن کنید. وقتی `فقط به حالت پراکسی` (Proxy only) انتخاب شده باشد، درخواست تا وقتی نمایه‌ای در حال اجراست از راه پروکسی فرستاده می‌شود. اگر هیچ نمایه‌ای اجرا نشود و `استفاده از پراکسی` روشن باشد، ساختن پیکربندی ناموفق می‌شود. از نسخهٔ 2.0.4، `فقط به حالت پراکسی` به‌تنهایی دیگر باعث شکست نمی‌شود: در این حالت درخواست مستقیم فرستاده می‌شود.
- برای روشن و خاموش کردن سریع WARP، `تنظیمات` (Settings) ← `مسیریابی` (Routing) ← `پروفایل مسیریابی فعلی` (Current routing profile) را باز کنید، سپس روی `فعال‌سازی WARP` (Enable WARP) یا `غیرفعال‌سازی WARP` (Disable WARP) بزنید. پیش از نسخهٔ 2.0.5، همین پنجره از منوی (⋮) صفحهٔ `پیکربندی` (Profiles) ← `پروفایل مسیریابی` (Routing profile) هم باز می‌شود.
- اگر WARP بدون پیکربندی ساخته‌شده روشن باشد، Android پیام «WARP is enabled but its config has not been generated. Generate it in Settings › Routing › WARP.» (WARP روشن است اما پیکربندی آن ساخته نشده است؛ آن را در تنظیمات › Routing › WARP بسازید.) را نشان می‌دهد.
- ویرایشگرهای نمایهٔ WireGuard و MASQUE یک دکمهٔ `ایجاد هویت WARP` (Generate WARP identity) دارند.

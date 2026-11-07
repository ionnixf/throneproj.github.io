+++
title = "لینک‌های عمیق"
description = "با لینک‌های throne:// اشتراک‌ها، نمایه‌های مسیریابی و نمایه‌های پروکسی را با یک کلیک در دسکتاپ و Android اضافه کنید."
weight = 70
toc = true
+++

لینک عمیق یک URL از نوع `throne://` است که به Throne می‌گوید یک اشتراک، یک نمایهٔ مسیریابی، نمایه‌های مسیریابی راه‌دور یا یک نمایهٔ پروکسی اضافه کند. ارائه‌دهندگان، لینک‌های عمیق را در وب‌سایت‌ها و کدهای QR قرار می‌دهند.

## Throne چگونه لینک عمیق را دریافت می‌کند {#receive}

کلیک روی لینک فقط وقتی کار می‌کند که `throne://` برای Throne ثبت شده باشد. نصب‌کنندهٔ Windows، بسته‌های `.deb` و `.rpm` و اسکریپت نصب در Linux، و `Throne.app` در macOS آن را برای حساب کاربری شما ثبت می‌کنند. نسخهٔ ZIP در Windows یا Linux این کار را نمی‌کند: `تنظیمات` (Settings) ← `تنظیمات پایه` (Basic Settings) را باز کنید، در زبانهٔ `متداول` (Common) گزینهٔ `Register throne:// links at startup` را تیک بزنید (فقط در حالت پیشرفته؛ [حالت ساده و پیشرفته](@/get_started/configuration.fa.md#interface-mode) را ببینید) و روی `تایید` (OK) کلیک کنید. اگر چند نسخه دارید، لینک‌ها در آخرین نسخه‌ای باز می‌شوند که با این گزینهٔ روشن اجرا شده است؛ `Install` در کنار آن، نسخهٔ فعلی را ثبت می‌کند.

بدون ثبت، لینک را کپی کنید و در پنجرهٔ اصلی `Ctrl+V` را بزنید، یا متن لینک را روی پنجره بکشید و رها کنید. Throne لینک را به‌عنوان [آرگومان خط فرمان](@/reference/files.fa.md#command-line) هم می‌پذیرد.

{% alert_info() %}
در دسکتاپ، `کد QR را اسکن کنید` (Scan QR Code) و تصویرهای QR که روی پنجره رها می‌شوند، فقط لینک‌های `add` را وارد می‌کنند. برای لینک عمیق دیگری که در یک کد QR است، متن پس از `QR Code Result:` را از زبانهٔ `گزارش‌ها` (Logs) کپی کنید و `Ctrl+V` را بزنید.
{% end %}

[Throne بدون رابط گرافیکی](@/headless/_index.fa.md) لینک‌های `throne://` را پردازش نمی‌کند. اشتراک را با `thronectl sub add` اضافه کنید و لینک‌های `add` را با `thronectl import` وارد کنید؛ [Throne API و thronectl](@/advanced/api.fa.md#commands) را ببینید.

## ساختار لینک {#format}

```text
throne://<command>/<payload>
```

- `throne://` را با حروف کوچک بنویسید. دستور به بزرگی و کوچکی حروف حساس نیست.
- اسلش پس از دستور الزامی است.
- Throne هر چیزی را که پس از `?` یا `#` در لینک بیاید نادیده می‌گیرد، پس همه چیز درون محتوا (payload) قرار می‌گیرد.
- اگر محتوا percent-encode شده باشد (برای مثال `%2B` به‌جای `+`)، Throne ابتدا آن را رمزگشایی می‌کند.

| دستور | کاربرد | اول می‌پرسد؟ |
| --- | --- | --- |
| `addsub` | یک گروه اشتراک اضافه و آن را به‌روز می‌کند | بله |
| `route` | یک نمایهٔ مسیریابی را که درون لینک است وارد می‌کند | بله |
| `remoteroute` | نمایه‌های مسیریابی راه‌دور را با URL اضافه می‌کند | بله |
| `add` | یک نمایهٔ پروکسی به گروه فعلی اضافه می‌کند | خیر. در Android، وقتی روی لینک در برنامهٔ دیگری بزنید، بله. |

### قوانین Base64 {#base64}

| دستور | محتوای پذیرفته‌شده |
| --- | --- |
| `addsub`، `remoteroute` | فقط Base64 استاندارد (`+` و `/`)، با یا بدون padding با `=`. Base64 سازگار با URL که `-` یا `_` داشته باشد ناموفق می‌شود. |
| `route`، `add` | Base64 سازگار با URL (`-` و `_`) یا Base64 استاندارد، با یا بدون padding. Throne خودش Base64 سازگار با URL و بدون padding می‌نویسد. |

محتوا را در یک خط و بدون فاصله نگه دارید. برای کدگذاری در Linux یا در Git Bash (در macOS، `-w 0` را حذف کنید):

```bash
printf '%s' 'https://example.com/sub/abc123#MyProvider' | base64 -w 0
```

در PowerShell:

```powershell
[Convert]::ToBase64String([Text.Encoding]::UTF8.GetBytes('https://example.com/sub/abc123#MyProvider'))
```

هر دو فرمان، Base64 استاندارد با padding چاپ می‌کنند.

## `addsub`: افزودن اشتراک {#addsub}

```text
throne://addsub/<base64>
```

محتوا، Base64 متن `<subscription_url>#<group_name>` است. URL را بدون تغییر بگذارید؛ لازم نیست آن را percent-encode کنید. بخش `#<group_name>` اختیاری است (پیش‌فرض: میزبان URL) و یک فاصلهٔ ساده هم در آن کار می‌کند. برای مثال، `https://example.com/sub/abc123#MyProvider` این لینک را می‌دهد:

```text
throne://addsub/aHR0cHM6Ly9leGFtcGxlLmNvbS9zdWIvYWJjMTIzI015UHJvdmlkZXI=
```

Throne ابتدا با یک کادر انتخاب `Auto update` تیک‌خورده می‌پرسد، سپس گروه را می‌سازد و آن را به‌روز می‌کند.

{% alert_info() %}
`Auto update` فقط تعیین می‌کند که گروه در به‌روزرسانی‌های خودکار شرکت کند. به‌روزرسانی‌های خودکار به `تنظیمات پایه` (Basic Settings) ← `اشتراک` (Subscription) ← `Subscription auto update` ← `فعال کردن` (Enable) هم نیاز دارند که به‌طور پیش‌فرض خاموش است. [اشتراک‌ها و گروه‌ها](@/guides/subscriptions.fa.md#auto-update) را ببینید.
{% end %}

## `route`: وارد کردن نمایهٔ مسیریابی {#route}

```text
throne://route/<base64>
```

یک نمایهٔ مسیریابی کامل را حمل می‌کند. برای ساختن آن، نمایه را در زبانهٔ `مسیر` (Route) از `تنظیمات مسیریابی` (Routing Settings) انتخاب کنید و روی `استخراج` (Export) کلیک کنید (`Ctrl+C`). نمایهٔ واردشده تا وقتی آن را در منوی `مسیریابی` (Routing) انتخاب نکنید فعال نمی‌شود.

محتوا، Base64 یک شیء JSON است:

```json
{
  "kind": "throne-route-profile",
  "v": 1,
  "name": "Example",
  "default_outbound": "proxy",
  "rules": []
}
```

```text
throne://route/eyJraW5kIjoidGhyb25lLXJvdXRlLXByb2ZpbGUiLCJ2IjoxLCJuYW1lIjoiRXhhbXBsZSIsImRlZmF1bHRfb3V0Ym91bmQiOiJwcm94eSIsInJ1bGVzIjpbXX0
```

- `default_outbound` یکی از `proxy`، `direct`، `block` یا `warp-bypass` است.
- یک نمایهٔ خام به‌جای `rules`، مقدار `"raw": true` و یک شیء `route` دارد.
- یک شیء `dns` اختیاری، تنظیمات DNS خود نمایه را حمل می‌کند: `remote_dns`، `remote_dns_disable_ipv6`، `direct_dns`، `direct_dns_disable_ipv6`، `dns_final_out`، `enable_dns_routing`، `fake_dns` و `fakeip_disable_ipv6` ([DNS برای هر نمایهٔ مسیریابی](@/guides/dns.fa.md#route-dns) را ببینید). یک قانون می‌تواند `"disabled": true` داشته باشد.
- قوانین، سرورهای ذخیره‌شده‌ای را که ترافیک را به آن‌ها می‌فرستند با نام مشخص می‌کنند. Throne دربارهٔ نام‌هایی که پیدا نکند به شما اطلاع می‌دهد.

`Import` (`Ctrl+V`) در زبانهٔ `مسیر` (Route)، محتوای Base64 به‌تنهایی یا JSON ساده را هم می‌پذیرد. [اشتراک‌گذاری نمایه‌ها](@/guides/routing.fa.md#share-profiles) را ببینید.

## `remoteroute`: افزودن نمایه‌های مسیریابی راه‌دور {#remoteroute}

```text
throne://remoteroute/<base64>
```

فقط URLها را حمل می‌کند: Throne هر نمایهٔ مسیریابی را دانلود می‌کند و می‌تواند آن را به‌روز نگه دارد. از آن برای توزیع نمایهٔ مسیریابی‌ای که خودتان نگهداری می‌کنید استفاده کنید. محتوا، Base64 یک فهرست ساده است، نه JSON، با یک URL در هر خط:

```text
<profile_url_1>#<name_1>
<profile_url_2>#<name_2>
```

- خط‌هایی که با `http://` یا `https://` شروع نشوند نادیده گرفته می‌شوند. `#<name>` اختیاری است (پیش‌فرض: میزبان URL).
- هر URL باید یک نمایهٔ مسیریابی ساخت‌یافته ارائه کند: یک لینک `throne://route/`، محتوای Base64 آن یا JSON آن. نمایه‌های خام نمی‌توانند راه‌دور باشند.

برای مثال، این فرمان:

```bash
printf '%s\n%s' 'https://example.com/routes/bypass-iran.json#BypassIran' 'https://example.com/routes/ads.json' | base64 -w 0
```

این لینک را می‌دهد:

```text
throne://remoteroute/aHR0cHM6Ly9leGFtcGxlLmNvbS9yb3V0ZXMvYnlwYXNzLWlyYW4uanNvbiNCeXBhc3NJcmFuCmh0dHBzOi8vZXhhbXBsZS5jb20vcm91dGVzL2Fkcy5qc29u
```

Throne ابتدا با یک کادر انتخاب `Auto update` تیک‌خورده برای همهٔ نمایه‌ها می‌پرسد، سپس آن‌ها را دانلود می‌کند.

- هر به‌روزرسانی، قوانین و خروجی پیش‌فرض نمایه را جایگزین می‌کند، نه نام آن را.
- به‌روزرسانی‌های زمان‌بندی‌شده به `تنظیمات پایه` (Basic Settings) ← `اشتراک` (Subscription) ← `Routing profiles auto update` ← `فعال کردن` (Enable) هم نیاز دارند (فقط در حالت پیشرفته) که به‌طور پیش‌فرض خاموش است. [نمایه‌های راه‌دور](@/guides/routing.fa.md#remote-profiles) را ببینید.
- URLهای روی `raw.githubusercontent.com` از راه `Remote Rule-set Mirror` دانلود می‌شوند که ممکن است تا مدتی پس از تغییر فایل، نسخهٔ قدیمی‌تری را ارائه کند.

## `add`: افزودن یک نمایهٔ پروکسی {#add}

```text
throne://add/<base64>
```

محتوا، Base64 تنظیمات خروجی نمایه به‌صورت JSON است. در دسکتاپ، نمایه بی‌درنگ به گروه فعلی اضافه می‌شود، درست مانند یک لینک `vless://` جای‌گذاری‌شده. لینک‌های `add` هر جا که لینک‌های اشتراک‌گذاری کار می‌کنند هم کار می‌کنند: درون اشتراک‌ها، در فایل‌ها و در کدهای QR.

برای ساختن آن، روی نمایه‌ها راست‌کلیک کنید ← `اشتراک گذاری` (Share) ← `Copy links of selected (Deep Links)` (`Ctrl+Alt+C`، فقط در حالت پیشرفته)، یا از `اشتراک گذاری` ← `کد QR و پیوند` (QR Code and link) با `Deep Link` تیک‌خورده استفاده کنید. لینک عمیق پیکربندی کامل نمایه را حمل می‌کند، از جمله گزینه‌های اتصال پیشرفته مانند bind interface. `لینک های انتخاب شده را کپی کنید` (Copy links of selected) با `Ctrl+C` فقط برای نمایه‌هایی که لینک اشتراک‌گذاری استاندارد ندارند لینک عمیق می‌دهد.

## در Android {#android}

Throne برای Android همین چهار دستور را پردازش می‌کند و لینک‌های `add` و `route` را هم می‌سازد. همچنین لینک‌های `clash://install-config?url=…&name=…` را می‌پذیرد که مانند `addsub` یک اشتراک اضافه می‌کنند؛ مقدار `url` را percent-encode کنید. طرح (scheme) `throne://` هنگام نصب برنامه ثبت می‌شود.

روی لینک در برنامهٔ دیگری بزنید، یا در صفحهٔ `پیکربندی` (Profiles) از `اضافه‌کردن پراکسی جدید` (Add profile) ← `وارد کردن از بریده‌دان` (Import from clipboard) یا `اسکن از کد QR` (Scan QR code) استفاده کنید. کدهای QR برای هر چهار دستور کار می‌کنند و لینک‌های `route` و `remoteroute` با `درون‌ریزی` (Import) در صفحهٔ `مسیریابی` (Routing) هم کار می‌کنند. تفاوت‌ها:

- لینک `add` که در برنامهٔ دیگری روی آن بزنید، ابتدا می‌پرسد. از کلیپ‌بورد یا کد QR، بدون پرسش وارد می‌شود.
- نمایه‌های مسیریابی خام (`"raw": true`) رد می‌شوند و نقطه‌های پایانی (endpoint) در یک نمایهٔ مسیریابی اشتراکی کنار گذاشته می‌شوند.
- به‌روزرسانی‌های زمان‌بندی‌شده به `تنظیمات` (Settings) ← `اشتراک‌ها` (Subscriptions) ← `به‌روزرسانی خودکار اشتراک` (Subscription auto update) و، برای نمایه‌های مسیریابی راه‌دور، به `تنظیمات` ← `مسیریابی` (Routing) ← `به‌روزرسانی خودکار پروفایل‌های راه دور` (Auto update remote profiles) نیاز دارند. هر دو به‌طور پیش‌فرض خاموش‌اند.

## عیب‌یابی {#troubleshooting}

این پیام‌ها مربوط به Throne دسکتاپ هستند.

- **کلیک روی لینک کاری نمی‌کند، یا مرورگر می‌پرسد از کدام برنامه استفاده شود.** `throne://` برای این نسخه ثبت نشده است؛ [Throne چگونه لینک عمیق را دریافت می‌کند](#receive) را ببینید. اگر چند نسخه دارید، در نسخه‌ای که باید لینک‌ها را باز کند `Install` را بزنید. یا لینک را کپی کنید و در Throne `Ctrl+V` را بزنید.
- **Linux: لینک‌ها هنوز باز نمی‌شوند.** بستهٔ `desktop-file-utils` را نصب کنید، سپس `Install` را در کنار `Register throne:// links at startup` بزنید.
- **هیچ اتفاقی نمی‌افتد و گزارش خالی می‌ماند.** Throne این‌ها را بدون پیام کنار می‌گذارد: محتوای `addsub` که Base64 استاندارد معتبر نیست (Base64 سازگار با URL با `-` یا `_`، فاصله یا شکست خط)، لینکی بدون اسلش پس از دستور (به‌جز `add` و `remoteroute`)، و دستور ناشناخته‌ای که محتوای آن رمزگشایی نمی‌شود.
- **«Ignored deeplink with unknown command: …» (لینک عمیق با دستور ناشناخته نادیده گرفته شد)** واژهٔ پس از `throne://` یکی از `addsub`، `route`، `remoteroute` یا `add` نیست.
- **«The link did not contain a subscription URL.» (لینک، URL اشتراک نداشت.)** در محتوای `addsub`، پیش از `#`، URL وجود ندارد.
- **«Deep link has no data» (لینک عمیق داده ندارد) یا «Base64 is invalid.» (Base64 نامعتبر است.)** پس از `throne://remoteroute/` چیزی نیامده، یا محتوا Base64 استاندارد نیست.
- **«The link did not contain any valid http(s) routing profile URLs.» (لینک هیچ URL معتبر http(s) برای نمایهٔ مسیریابی نداشت.)** هیچ خطی از محتوای `remoteroute` با `http://` یا `https://` شروع نمی‌شود. فهرستی از URLها بفرستید، نه JSON.
- **«The link did not contain any valid remote routing profiles.» (لینک هیچ نمایهٔ مسیریابی راه‌دور معتبری نداشت.)** لینک پس از `remoteroute` اسلش ندارد.
- **«The link could not be parsed:» (لینک قابل تجزیه نبود:)** (لینک‌های `route`). «Empty input» (ورودی خالی): پس از `throne://route/` چیزی نیامده است. «Input is not valid JSON, base64, or a Throne route link» (ورودی، JSON، base64 یا لینک route معتبر Throne نیست): محتوا آسیب دیده یا ناقص است. «Unrecognized route object» (شیء route ناشناخته): JSON مقدار `"kind": "throne-route-profile"` را ندارد.
- **«0 نمایه وارد شد» (Imported 0 profile(s)) در گزارش.** محتوای یک لینک `add` آسیب دیده است، یا کد QR اسکن‌شده نوع دیگری از لینک عمیق دارد.
- **«Remote routing profile … failed: …» (نمایهٔ مسیریابی راه‌دور … ناموفق بود: …) در گزارش.** نمایه اضافه شد، اما URL آن دانلود نمی‌شود یا نمایهٔ مسیریابی ساخت‌یافته‌ای ارائه نمی‌کند. آن را اصلاح کنید، سپس روی `Update` در زبانهٔ `مسیر` (Route) کلیک کنید.

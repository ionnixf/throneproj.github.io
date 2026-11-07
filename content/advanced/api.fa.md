+++
title = "Throne API و thronectl"
description = "Throne را با Throne API و کلاینت خط فرمان آن، thronectl، از اسکریپت‌ها، کلیدهای میانبر و دستگاه‌های دیگر کنترل و مدیریت کنید."
weight = 80
toc = true
+++

Throne API به اسکریپت‌ها، ابزارهای کلید میانبر و دستگاه‌های دیگر اجازه می‌دهد Throne دسکتاپ را کنترل و مدیریت کنند. `thronectl` کلاینت خط فرمان این API است و `thronectl web` یک [رابط وب](@/headless/web.fa.md) ارائه می‌دهد. برای نوشتن کلاینت خودتان، از کتابخانهٔ Go و [مشخصات API](https://github.com/throneproj/Throne-api/blob/main/spec/api.md) در مخزن [Throne-api](https://github.com/throneproj/Throne-api) استفاده کنید.

[Throne بدون رابط گرافیکی](@/headless/_index.fa.md) API را از همان ابتدا روشن دارد؛ [مدیریت با thronectl](@/headless/thronectl.fa.md) را ببینید. Throne برای Android، Throne API ندارد.

## روشن کردن API در Throne {#enable}

1. `تنظیمات` (Settings) ← `Integration Settings` را باز کنید (فقط در حالت پیشرفته؛ [حالت ساده و پیشرفته](@/get_started/configuration.fa.md#interface-mode) را ببینید)، سپس زبانهٔ `Throne API` را.
2. `Enable the Throne API` را تیک بزنید. Throne یک کلید جدید در فیلد می‌گذارد.
3. روی `تایید` (OK) کلیک کنید.

API روی `127.0.0.1:9095` گوش می‌دهد، بنابراین فقط برنامه‌های همین رایانه می‌توانند متصل شوند. برای اینکه دستگاه‌های دیگر هم متصل شوند، `Listen on` را روی `Local network` بگذارید و آن دستگاه‌ها را در `Allowed addresses` فهرست کنید، مانند `192.168.1.0/24, 10.0.0.5` (اگر خالی باشد، همهٔ نشانی‌ها می‌توانند متصل شوند). `اجازهٔ مدیریت از شبکهٔ محلی` (Allow management from the local network) را فقط وقتی تیک بزنید که آن دستگاه‌ها باید [درخواست‌های مدیریتی](#admin-requests) هم بفرستند.

`Copy for scripts` دو تنظیمی را که `thronectl` می‌خواند، یعنی `THRONE_ADDR` و `THRONE_KEY`، کپی می‌کند. `Regenerate` کلید جدیدی می‌سازد و اسکریپت‌هایی که کلید قدیمی را دارند از کار می‌افتند. [کیل‌سوییچ](@/advanced/kill_switch.fa.md) جلوی کلاینت‌های API را نمی‌گیرد.

## نصب thronectl {#install-thronectl}

بسته‌های `.deb` و `.rpm` و فایل ZIP مخصوص Linux برنامهٔ Throne، و ایمیج Docker مربوط به Throne بدون رابط گرافیکی، `thronectl` را دارند. در غیر این صورت، آن را برای Linux، Windows، macOS یا FreeBSD از [انتشارهای Throne-api](https://github.com/throneproj/Throne-api/releases) دانلود کنید و در پوشه‌ای قرار دهید که در `PATH` شماست، یا با Go نصبش کنید:

```bash
go install github.com/throneproj/Throne-api/cmd/thronectl@latest
```

## اتصال thronectl به Throne {#connect}

`thronectl` نشانی را از `THRONE_ADDR` (پیش‌فرض: `127.0.0.1:9095`) و کلید را از `THRONE_KEY`، یا از فایلی که `THRONE_KEY_FILE` نام می‌برد، می‌خواند. `thronectl` هرگز کلید را به‌عنوان آرگومان نمی‌پذیرد، پس آن را در فایلی نگه دارید که فقط خودتان بتوانید بخوانید:

1. در Throne، روی `Copy for scripts` کلیک کنید.
2. فقط خود کلید را، بدون `THRONE_KEY=`، در یک فایل ذخیره کنید، برای مثال `~/.config/throne/api-key`. در Linux و macOS، `chmod 600` را روی فایل اجرا کنید.
3. متغیرها را تنظیم کنید، برای مثال در `~/.bashrc`:

   ```bash
   export THRONE_ADDR=127.0.0.1:9095
   export THRONE_KEY_FILE=~/.config/throne/api-key
   ```

   در PowerShell (`$PROFILE`):

   ```powershell
   $env:THRONE_ADDR = "127.0.0.1:9095"
   $env:THRONE_KEY_FILE = "$HOME\throne-api-key.txt"
   ```

4. اتصال را با `thronectl status` آزمایش کنید.

پرچم‌ها (flags) پیش یا پس از فرمان کار می‌کنند: `--addr host:port` و `--key-file file` بر متغیرها اولویت دارند، `--json` به‌جای جدول، JSON چاپ می‌کند و `--timeout 30s` مدت انتظار را تعیین می‌کند (با `0`، تا هر وقت Throne لازم داشته باشد منتظر می‌ماند). در systemd، `thronectl` کلید را از credential به نام `api.key` (`LoadCredential=api.key:/path/to/key`) هم می‌گیرد. برای Throne بدون رابط گرافیکی، [اتصال thronectl به Throne](@/headless/thronectl.fa.md#connect) را ببینید.

## فرمان‌ها {#commands}

`thronectl help <command>` یک فرمان را توضیح می‌دهد. فرمان‌ها شناسه‌هایی را می‌گیرند که فهرست‌ها نشان می‌دهند.

| فرمان | کاری که انجام می‌دهد |
| --- | --- |
| `status` | وضعیت، نمایهٔ در حال اجرا، حالت‌ها، نمایهٔ مسیریابی، نسخهٔ Throne و، اگر برنامهٔ دیگری TUN را در اختیار داشته باشد، نام آن. |
| `groups` | گروه‌ها را همراه با نمایهٔ مسیریابی خودشان فهرست می‌کند. |
| `profiles [--group N] [--name REGEX] [--ids 1,2]` | نمایه‌ها را با آخرین نتایج تستشان فهرست می‌کند. |
| `connect <id>` | یک نمایه را اجرا می‌کند. اگر نمایه از قبل در حال اجرا باشد، `--restart` آن را دوباره اجرا می‌کند. |
| `connect last` | نمایهٔ در حال اجرا را اجرا می‌کند، وگرنه آخرین نمایه را، وگرنه نمایهٔ به‌خاطرسپرده‌شده را. |
| `connect best [--group N] [--name REGEX] [--max-age 1h]` | نمایه‌ای را که کمترین تأخیر را دارد اجرا می‌کند و پیش از آن، نمایه‌هایی را تست می‌کند که نتیجه‌ای جدیدتر از `--max-age` ندارند. |
| `disconnect`، `toggle` | نمایهٔ در حال اجرا را متوقف می‌کند؛ `toggle` وقتی چیزی اجرا نمی‌شود، `connect last` را اجرا می‌کند. |
| `tun on`، `tun off`، `tun toggle` | حالت TUN. |
| `proxy on`، `proxy off`، `proxy toggle` | پروکسی سیستمی. |
| `routes`، `route <id>` | نمایه‌های مسیریابی را فهرست می‌کند؛ یکی را فعال می‌کند و نمایهٔ در حال اجرا را دوباره اجرا می‌کند. |
| `test latency`، `test ip`، `test speed` | یک تست URL، تست IP یا تست سرعت را روی `--group N` (با `--name REGEX` اختیاری) یا روی شناسهٔ نمایه‌ها اجرا می‌کند. |
| `jobs`، `job <id>`، `cancel <id>` | تست‌های اخیر را فهرست می‌کند، یکی را نشان می‌دهد، یکی را متوقف می‌کند. |
| `clear` | نتایج تست را پاک می‌کند؛ نمایه‌ها مانند `test` انتخاب می‌شوند. |
| `live`، `traffic [--by profile] [--by app] [--since 24h]` | نرخ فعلی ترافیک؛ مجموع ترافیک. `--since` روز را هم می‌پذیرد، مانند `7d`. |
| `logs [-n N] [-f]` | آخرین `N` خط گزارش (پیش‌فرض 200، حداکثر 1000). `-f` خطوط جدید را دنبال می‌کند. |
| `import [--group N] FILE` | لینک‌ها، یک اشتراک، یا یک پیکربندی Clash، sing-box، Xray، WireGuard یا OpenVPN تا حجم 900 KiB را وارد می‌کند. `-` ورودی استاندارد را می‌خواند؛ `--url URL` باعث می‌شود Throne یک URL را یک بار دانلود کند. |
| `sub add <url> [--name NAME]` | یک گروه اشتراک اضافه می‌کند و آن را به‌روز می‌کند. گزینه‌ها: `--no-auto-update`، `--no-update`، `--route ID` (نمایهٔ مسیریابی خود گروه)، `--import-route` (نگه داشتن نمایهٔ مسیریابی‌ای که اشتراک فراهم می‌کند). |
| `sub update <id>…`، `sub update --all` | گروه‌های اشتراک را به‌روز می‌کند، یا همهٔ گروه‌هایی را که آرشیو نشده‌اند. |
| `group add <name>`، `group rm <id>` | یک گروه اضافه می‌کند؛ یک گروه را همراه با نمایه‌هایش حذف می‌کند. |
| `profile rm <id>…`، `profile rm --group N [--name REGEX]` | نمایه‌ها را حذف می‌کند. |
| `settings [get] [KEY…]`، `settings set KEY=VALUE…` | [تنظیمات](#settings) را نشان می‌دهد یا تغییر می‌دهد. |
| `backup create [-o FILE] [--parts LIST]` | یک آرشیو `.thrbackup` می‌نویسد. |
| `backup restore FILE [--parts LIST] [--yes]` | محتوای آرشیو را نشان می‌دهد، می‌پرسد، آن را بازیابی می‌کند و Throne را دوباره راه‌اندازی می‌کند. بخش‌های پیش‌فرض: `profiles,routes,otp,ip_lists`. |
| `rulesets update` | مجموعه‌قوانین راه‌دور نمایهٔ در حال اجرا را دوباره دانلود می‌کند. |
| `geo`، `geo update [geoip] [geosite] [--only-missing]` | فایل‌های geo مربوط به Xray را نشان می‌دهد؛ آن‌ها را دوباره دانلود می‌کند. |
| `restart`، `shutdown [--yes]` | Throne را دوباره راه‌اندازی می‌کند؛ پس از پرسیدن، از Throne خارج می‌شود. |
| `web [--listen ADDR] …`، `web passwd` | [رابط وب](@/headless/web.fa.md) را ارائه می‌دهد؛ گذرواژهٔ آن را تنظیم می‌کند. |
| `keygen [-o FILE]` | یک کلید جدید می‌سازد و آن را چاپ می‌کند، یا در فایل جدیدی می‌نویسد که فقط خودتان بتوانید بخوانید. به Throne متصل نمی‌شود. |
| `call <METHOD> <PATH> [key=value…] [--body JSON]` | هر درخواستی از [مشخصات API](https://github.com/throneproj/Throne-api/blob/main/spec/api.md) را می‌فرستد. |
| `completion bash`، `zsh`، `fish` یا `powershell` | یک اسکریپت [تکمیل خودکار](#completion) چاپ می‌کند. |
| `version` | نسخهٔ `thronectl` را چاپ می‌کند. |

- بدون `--group`، `connect best` و `import` از گروهی استفاده می‌کنند که Throne نشان می‌دهد.
- `--name` یک عبارت باقاعده (regular expression) است که به بزرگی و کوچکی حروف حساس نیست و می‌تواند با هر بخشی از نام منطبق شود. آن را داخل نقل‌قول بگذارید: `--name 'DE|NL'`.
- `--parts` فهرستی جداشده با ویرگول از `profiles`، `routes`، `settings`، `otp`، `ip_lists` و `icons` را می‌پذیرد.
- فرمان‌ها تا `--timeout` منتظر نتیجه می‌مانند؛ اگر Throne بیشتر طول بکشد، کار را خودش تمام می‌کند. با `--no-wait`، فرمان بی‌درنگ برمی‌گردد. به تست‌ها و دانلود فایل‌های geo زمان بیشتری بدهید، برای مثال `--timeout 15m`. Throne در هر لحظه فقط یک تست اجرا می‌کند.
- `route` گروه‌هایی را که [نمایهٔ مسیریابی خودشان](@/guides/routing.fa.md#group-routing) را دارند تغییر نمی‌دهد. از راه API، گروه فقط با `sub add` نمایهٔ مسیریابی خودش را می‌گیرد؛ برای تغییر آن در آینده، از گزینه‌های گروه در Throne استفاده کنید.
- کدهای خروج: `0` موفقیت؛ `1` خطایی از Throne (`error: <code>: <message>`)، به‌روزرسانی ناموفق، یا پرسشی که به آن پاسخ منفی داده شود؛ `2` فرمان یا پرچم نادرست؛ `3` نبود اتصال، کلید ردشده، یا فایلی که خوانده نمی‌شود.

### تنظیمات {#settings}

`thronectl settings set log_level=debug kill_switch=true` تنظیمات را تغییر می‌دهد. `true`، `false` و اعداد به همان شکل فرستاده می‌شوند و هر چیز دیگری به‌صورت متن. اگر یکی از مقدارها نادرست باشد، Throne هیچ چیز را تغییر نمی‌دهد.

| بخش | تنظیمات |
| --- | --- |
| شروع | `remember_enable` |
| گزارش‌ها | `log_level`، `xray_log_level` |
| ورودی Mixed | `inbound_enabled`، `inbound_address`، `inbound_port` |
| تست‌ها | `test_url`، `test_concurrency`، `test_timeout_ms` |
| به‌روزرسانی‌های خودکار | `sub_auto_update`، `sub_respect_server_interval`، `route_auto_update` (بر حسب دقیقه، `0` یعنی خاموش) |
| کیل‌سوییچ | `kill_switch` |
| TUN | `tun_strict_route`، `tun_auto_redirect` (Linux)، `tun_mtu`، `tun_ipv6`، `tun_ipv4_cidr`، `tun_ipv6_cidr` |
| پورت‌ها و APIها | `dns_in_port`، `dns_in_lan` ([سرور DNS برای دستگاه‌های دیگر](@/guides/dns.fa.md#dns-server))، `clash_api_port`، `clash_listen_addr`، `singbox_api_port` |
| DNS و زمان | `remote_dns`، `direct_dns`، `ntp_enabled`، `ntp_server`، `ntp_interval` |

بیشتر تغییرات وقتی اعمال می‌شوند که نمایهٔ در حال اجرا دوباره اجرا شود (تنظیمات TUN، وقتی حالت TUN دوباره شروع شود). `thronectl status` می‌گوید چه وقت راه‌اندازی دوباره لازم است و `thronectl connect last --restart` آن را انجام می‌دهد. مقدارهای مجاز در [جدول تنظیمات](https://github.com/throneproj/Throne-api/blob/main/spec/api.md#settings) مشخصات API آمده‌اند.

## درخواست‌های مدیریتی {#admin-requests}

`logs`، `import`، `sub add`، `group add`، `group rm`، `profile rm`، `settings set`، `backup`، `restart` و `shutdown` درخواست مدیریتی می‌فرستند (`logs` به این دلیل که گزارش، سایت‌هایی را که بازدید کرده‌اید نام می‌برد). فرمان‌های دیگر درخواست کنترلی می‌فرستند، با همان کلید.

Throne فقط از رایانهٔ خودش (`127.0.0.1` یا `::1`) به درخواست‌های مدیریتی پاسخ می‌دهد. از دستگاه‌های دیگر، این درخواست‌ها با `forbidden` شکست می‌خورند، مگر اینکه `اجازهٔ مدیریت از شبکهٔ محلی` روشن باشد (برای Throne بدون رابط گرافیکی، `--api-admin-lan`).

- آرشیوهای پشتیبان، گذرواژه‌ها و کلیدهای نمایه‌های شما و رمزهای OTP شما را به‌صورت متن ساده نگه می‌دارند. `thronectl` آن‌ها را طوری می‌سازد که فقط خودتان بتوانید بخوانید؛ همین‌طور نگهشان دارید و وقتی دیگر لازمشان ندارید، حذفشان کنید.
- `backup restore` آرشیوی را که نمایه‌هایش برنامه اجرا می‌کنند یا فایل می‌خوانند، مانند نمایه‌های هستهٔ اضافی (Extra Core)، رد می‌کند (`invalid_archive`). چنین آرشیوی را در پنجرهٔ Throne بازیابی کنید.
- `backup restore` و `shutdown` پیش از انجام کار می‌پرسند. در اسکریپتی که ترمینال ندارد، `--yes` را اضافه کنید.
- بازیابی `settings` تنظیماتی را که به این رایانه تعلق دارند، مانند Throne API، پورت‌ها، TUN و کیل‌سوییچ، نگه می‌دارد.

## نمونه‌ها {#examples}

```bash
thronectl profiles --group 3 --name 'DE|NL'
thronectl connect 12
thronectl --timeout 2m test latency --group 3
thronectl sub add 'https://sub.example.com/abc' --name Provider
thronectl connect best --max-age 10m   # retest results older than 10 minutes
```

روی نمایه‌های گروه 3 که نامشان `DE` یا `NL` دارد تست سرعت اجرا کنید، سپس سریع‌ترین را اجرا کنید (به [jq](https://jqlang.org/) نیاز دارد):

```bash
best=$(thronectl --json --timeout 15m test speed --group 3 --name 'DE|NL' |
  jq -r '[.results[] | select(.download_bps > 0)] | max_by(.download_bps) | .id // empty')
[ -n "$best" ] && thronectl connect "$best"
```

در PowerShell:

```powershell
$job = thronectl --json --timeout 15m test speed --group 3 --name 'DE|NL' | ConvertFrom-Json
$best = $job.results | Where-Object download_bps -gt 0 | Sort-Object download_bps -Descending | Select-Object -First 1
if ($best) { thronectl connect $best.id }
```

### یک کلید برای اتصال و قطع اتصال {#example-hotkey}

یک Stream Deck، صفحه‌کلید ماکرو (macro pad) یا ابزار کلید میانبر را طوری تنظیم کنید که `thronectl toggle` را اجرا کند. این ابزارها اغلب تنظیمات پوستهٔ شما را نمی‌خوانند: `THRONE_KEY_FILE` (و در صورت نیاز `THRONE_ADDR`) را به‌عنوان متغیر محیطی حساب کاربری خود تنظیم کنید، یا `--key-file` را اضافه کنید. Throne برای `Start/Stop Profile` و `Toggle Tun Mode` [کلیدهای میانبر سراسری](@/reference/shortcuts.fa.md#global-hotkeys) هم دارد.

## تکمیل خودکار در پوسته {#completion}

`thronectl completion <shell>` اسکریپتی چاپ می‌کند که فرمان‌ها، پرچم‌ها و نام تنظیمات را کامل می‌کند، و وقتی Throne در کمتر از یک ثانیه پاسخ دهد، شناسهٔ نمایه‌ها، گروه‌ها، نمایه‌های مسیریابی و تست‌ها را هم.

```bash
# bash: in ~/.bashrc (on macOS, ~/.bash_profile)
eval "$(thronectl completion bash)"
# zsh: in ~/.zshrc
autoload -Uz compinit && compinit
source <(thronectl completion zsh)
# fish
thronectl completion fish > ~/.config/fish/completions/thronectl.fish
```

```powershell
# In $PROFILE
thronectl completion powershell | Out-String | Invoke-Expression
```

## امنیت {#security}

- هر کس کلید را داشته باشد، اتصال، حالت‌ها و مسیریابی Throne را کنترل می‌کند و می‌تواند نام، نشانی، نتایج تست و ترافیک نمایه‌های شما را بخواند. هر جا [درخواست‌های مدیریتی](#admin-requests) مجاز باشند، با کلید پیکربندی نمایه‌ها و پشتیبان‌ها را هم می‌توان خواند که اطلاعات ورود شما را به‌صورت متن ساده دارند.
- کلید را در خط فرمان‌ها و اسکریپت‌هایی که دیگران می‌توانند بخوانند قرار ندهید: از فایل کلیدی استفاده کنید که فقط خودتان بتوانید بخوانید، یا از `THRONE_KEY` که از یک انبار اسرار (secret store) می‌آید.
- ترافیک میان `thronectl` و Throne با کلید رمزگذاری و احراز هویت می‌شود، اما محرمانگی پیشرو (forward secrecy) ندارد: هر کس کلید را به دست آورد، می‌تواند ترافیکی را که پیش‌تر ضبط شده است بخواند.
- اگر کلید فاش شد، روی `Regenerate` کلیک کنید و کلید جدید را به اسکریپت‌هایتان بدهید. برای Throne بدون رابط گرافیکی، [محافظت در برابر قطع دسترسی و بازیابی](@/headless/recovery.fa.md) را ببینید.
- `Listen on` را روی `This computer only` نگه دارید، مگر اینکه API را روی شبکه لازم داشته باشید.
- پس از 5 تلاش ناموفق در یک دقیقه، Throne آن نشانی را به مدت 60 ثانیه رد می‌کند؛ کلاینت‌های دیگری هم که پشت همان روتر هستند در این مدت نمی‌توانند متصل شوند. Throne هرگز `127.0.0.1` را رد نمی‌کند، و نه نشانی‌ای را که در 10 دقیقهٔ گذشته کلاینتی با موفقیت از آن وارد شده است.
- Throne هر درخواستی را که چیزی را تغییر می‌دهد، همراه با نشانی کلاینت در گزارش می‌نویسد.

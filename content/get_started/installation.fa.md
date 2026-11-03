+++
title = "نصب"
description = "Throne را روی Windows، macOS یا Linux نصب کنید، آن را به‌روز نگه دارید یا از سورس بسازید."
weight = 1
toc = true
+++

این صفحه توضیح می‌دهد که چگونه Throne را روی Windows، macOS و Linux نصب کنید، چگونه آن را به‌روزرسانی کنید و چگونه آن را از سورس بسازید. فایل‌ها را از صفحهٔ [دانلود](@/downloads.fa.md) دریافت کنید. برای Android، [نصب و ارتقا](@/android/installation.fa.md) را ببینید.

## Windows {#windows}

### کدام فایل را دانلود کنم {#windows-files}

| رایانهٔ شما | فایل |
|---|---|
| هر رایانهٔ Windows، اگر مطمئن نیستید | `Throne-<version>-windows-universal-installer.exe` |
| Windows 10 1809 یا جدیدتر، x64 | `Throne-<version>-windows64.zip` |
| Windows روی ARM64 | `Throne-<version>-windows-arm64.zip` |
| Windows 7 SP1 تا Windows 10 1803 (x64)، یا پردازنده‌ای بدون SSE4.2 | `Throne-<version>-windowslegacy64.zip` |
| Windows 7 SP1 یا جدیدتر، 32 بیتی (x86) | `Throne-<version>-windows32.zip` |

### نصب‌کننده {#windows-installer}

نصب‌کنندهٔ universal ساده‌ترین گزینه است:

- Throne را برای حساب کاربری شما در `%LOCALAPPDATA%\Throne` نصب می‌کند. به دسترسی مدیر (administrator) نیازی ندارید.
- بیلد مناسب رایانهٔ شما را انتخاب می‌کند: x64، ARM64 یا 32 بیتی. روی Windows x64 قدیمی‌تر از Windows 10 1809، بیلد legacy را نصب می‌کند.
- Throne را به منوی Start و به دسکتاپ اضافه می‌کند.
- Throne نصب‌شده در هر بار اجرا لینک‌های `throne://` را ثبت می‌کند تا این لینک‌ها در Throne باز شوند. [لینک‌های عمیق](@/advanced/deeplinks.fa.md#receive) را ببینید.

Throne نمایه‌ها و تنظیمات شما را در پوشهٔ `config` داخل پوشهٔ نصب نگه می‌دارد. اگر Throne را با نصب‌کنندهٔ قدیمی (پیش از 1.3.0) نصب کرده بودید، نصب‌کنندهٔ جدید از همان پوشه استفاده می‌کند و داده‌های شما را نگه می‌دارد.

برنامهٔ نصب می‌تواند Throne را برای همهٔ کاربران هم در `Program Files` نصب کند. این کار به دسترسی مدیر نیاز دارد. Throne نمی‌تواند در `Program Files` بنویسد، بنابراین داده‌های شما را به‌جای آن در `%LOCALAPPDATA%\Throne\config` نگه می‌دارد و به‌روزرسانی با یک کلیک در دسترس نیست.

برای حذف Throne، آن را از فهرست برنامه‌های نصب‌شده در Windows حذف کنید. برنامهٔ حذف می‌پرسد که آیا نمایه‌ها، تنظیمات و گزارش‌های شما هم پاک شوند یا نه. این برنامه ثبت لینک‌های `throne://` را هم حذف می‌کند.

### ZIP قابل حمل {#windows-zip}

1. فایل ZIP مناسب سیستم خود را دانلود کنید (جدول بالا را ببینید).
2. آن را در پوشه‌ای استخراج کنید که بتوانید در آن فایل بنویسید، برای مثال پوشه‌ای در پوشهٔ کاربری‌تان.
3. پوشهٔ `Throne` را باز کنید و `Throne.exe` را اجرا کنید.

همهٔ فایل‌های این پوشه را کنار هم نگه دارید. Throne داده‌های شما را در پوشهٔ `config` در کنار `Throne.exe` ذخیره می‌کند، بنابراین می‌توانید کل پوشه را جابه‌جا کنید یا از آن پشتیبان بگیرید. اگر Throne نتواند در پوشهٔ خودش بنویسد، به‌جای آن از `%LOCALAPPDATA%\Throne\config` استفاده می‌کند.

نسخه‌های ZIP به‌طور پیش‌فرض لینک‌های `throne://` را ثبت نمی‌کنند. برای روشن کردن این قابلیت، گزینهٔ `تنظیمات` (Settings) ← `تنظیمات پایه` (Basic Settings) ← `متداول` (Common) ← `Register throne:// links at startup` را تیک بزنید (فقط در حالت پیشرفته؛ [حالت ساده و پیشرفته](@/get_started/configuration.fa.md#interface-mode) را ببینید).

## macOS {#macos}

| Mac شما | فایل |
|---|---|
| Apple Silicon، macOS 14.4 یا جدیدتر | `Throne-<version>-macos-arm64.zip` |
| Intel، macOS 14.4 یا جدیدتر | `Throne-<version>-macos-amd64.zip` |
| Intel، macOS 10.15 تا 14.3 | `Throne-<version>-macoslegacy-amd64.zip` |

از نسخهٔ 1.4.0، بیلدهای معمولی به macOS 14.4 یا جدیدتر نیاز دارند؛ در Throne 1.3 و نسخه‌های قدیمی‌تر، این بیلدها به macOS 13 یا جدیدتر نیاز داشتند.

می‌توانید Throne را با [Homebrew](#homebrew) هم نصب کنید. Homebrew مراحل زیر را برایتان انجام می‌دهد و Throne را با `brew upgrade` به‌روز می‌کند.

1. فایل ZIP را دانلود و استخراج کنید. این فایل شامل پوشهٔ `Throne` با `Throne.app` است.
2. پیش از اینکه `Throne.app` را برای نخستین بار باز کنید، آن را به `/Applications` منتقل کنید. برای حالت TUN، Throne از طریق Terminal به هستهٔ خود دسترسی root می‌دهد و اگر برنامه در `~/Downloads` باشد، این کار ممکن است شکست بخورد.
3. Throne با گواهی Apple امضا نشده است، بنابراین پرچم قرنطینه (quarantine) را در Terminal حذف کنید. بدون این مرحله، macOS می‌گوید Throne «is damaged and can't be opened» (آسیب دیده است و باز نمی‌شود).

   ```bash
   xattr -d com.apple.quarantine /Applications/Throne.app
   ```

4. Throne را باز کنید.

Throne داده‌های شما را در پوشهٔ کاربری‌تان نگه می‌دارد، نه داخل `Throne.app`، بنابراین می‌توانید برنامه را بدون از دست دادن نمایه‌هایتان جایگزین کنید. `تنظیمات` (Settings) ← `پوشه Config باز شود` (Open Config Folder) این پوشه را نشان می‌دهد (فقط در حالت پیشرفته). حالت TUN به یک حساب مدیر نیاز دارد؛ [حالت TUN](@/guides/tun_mode.fa.md#privileges) را ببینید.

## Linux {#linux}

| فایل | کاربرد |
|---|---|
| اسکریپت نصب (پایین‌تر) | هر توزیعی. بیلد ZIP را در `/opt/Throne` نصب می‌کند. |
| `Throne-<version>-linux-amd64.zip`، `…-linux-arm64.zip` | یک نسخهٔ قابل حمل در پوشه‌ای به انتخاب شما |
| `Throne-<version>-debian-amd64.deb`، `…-debian-arm64.deb` | Debian، Ubuntu، Linux Mint و دیگر سیستم‌های مبتنی بر Debian |
| `Throne-<version>-fedora-amd64.rpm`، `…-fedora-arm64.rpm` | Fedora و سیستم‌های مبتنی بر RHEL (از نسخهٔ 1.3.0) |
| فایل‌هایی که به `-system-qt.deb` یا `-system-qt.rpm` ختم می‌شوند | همان بسته‌ها، اما از کتابخانه‌های Qt سیستم شما استفاده می‌کنند |
| [مخزن APT](#apt-repository) و [مخزن RPM](#rpm-repository) | بسته‌های system-Qt، که مدیر بستهٔ شما آن‌ها را به‌روز نگه می‌دارد |

از نسخهٔ 1.4.0، فایل‌های ZIP، اسکریپت نصب و بسته‌های معمولی `.deb` و `.rpm` به glibc 2.28 یا جدیدتر نیاز دارند. این‌ها روی Debian 10، Ubuntu 20.04، RHEL 8 (و AlmaLinux یا Rocky Linux 8)، Raspberry Pi OS (64 بیتی) bullseye و سیستم‌های جدیدتر اجرا می‌شوند. بسته‌های `-system-qt` به glibc 2.34 یا جدیدتر و Qt 6 توزیع شما نیاز دارند، برای نمونه روی Debian 12 یا Ubuntu 22.04. پیش از نسخهٔ 1.4.0، همهٔ بیلدهای Linux به glibc 2.34 یا جدیدتر نیاز داشتند و فایل‌های ARM64 که Qt را همراه دارند به glibc 2.38 یا جدیدتر.

{% alert_warning() %}
Throne را با `sudo` یا به‌عنوان root اجرا نکنید. برای حالت TUN، Throne فقط به هستهٔ خود، یعنی `ThroneCore`، دسترسی root می‌دهد و گذرواژهٔ شما را از طریق `pkexec` می‌پرسد. [حالت TUN](@/guides/tun_mode.fa.md#privileges) را ببینید.
{% end %}

### اسکریپت نصب {#linux-script}

این اسکریپت بیلد مناسب پردازندهٔ شما را دانلود می‌کند، آن را در `/opt/Throne` نصب می‌کند و Throne را به منوی برنامه‌ها اضافه می‌کند. به Python 3 و دسترسی root نیاز دارد:

```bash
curl -fsSL https://raw.githubusercontent.com/throneproj/Throne/dev/script/install_linux.py | sudo python3
```

اسکریپت آخرین نسخه‌های پایدار و ناپایدار را نشان می‌دهد، و اگر نسخه‌ای نصب شده باشد، نسخهٔ نصب‌شده را هم. `Install` یا `Uninstall` را انتخاب کنید. برای `Install`، شاخه را هم انتخاب کنید: `Stable` یا `Unstable`.

برای به‌روزرسانی یا حذف Throne، همین دستور را دوباره اجرا کنید. حذف، `/opt/Throne` و گزینهٔ منو را پاک می‌کند، اما تنظیمات شما را در `~/.config/Throne` نگه می‌دارد.

### ZIP قابل حمل {#linux-zip}

فایل ZIP شامل پوشهٔ `Throne` است. Throne داده‌های شما را در پوشهٔ `config` داخل آن نگه می‌دارد.

```bash
unzip Throne-x.x.x-linux-amd64.zip
cd Throne
./Throne
```

### Debian و Ubuntu (.deb) {#deb}

```bash
sudo apt install ./Throne-x.x.x-debian-amd64.deb
```

برای دریافت نسخه‌های جدید با `apt upgrade`، به‌جای آن از [مخزن APT](#apt-repository) استفاده کنید.

### Fedora و RHEL (.rpm) {#rpm}

```bash
sudo dnf install ./Throne-x.x.x-fedora-amd64.rpm
```

برای دریافت نسخه‌های جدید با `dnf upgrade` یا `zypper update`، به‌جای آن از [مخزن RPM](#rpm-repository) استفاده کنید.

هر دو بسته Throne را در `/opt/Throne` نصب می‌کنند و آن را به منوی برنامه‌ها اضافه می‌کنند. در این حالت Throne داده‌های شما را در `~/.config/Throne` نگه می‌دارد.

از نسخهٔ 1.4.0، این بسته‌ها یک سرویس systemd برای [Throne بدون رابط گرافیکی](@/headless/_index.fa.md)، یعنی `/usr/lib/systemd/system/throne.service`، و فایل تنظیمات آن، `/etc/throne/throne.env`، را هم نصب می‌کنند. بسته هرگز این سرویس را فعال یا اجرا نمی‌کند، بنابراین کار پنجرهٔ Throne را تغییر نمی‌دهد. [نصب و سرویس‌ها](@/headless/installation.fa.md#linux-packages) را ببینید.

### مخزن APT {#apt-repository}

مخزن APT در `throneproj.github.io/Throne/apt` بسته‌های system-Qt را برای amd64 و arm64 دارد ([بسته‌های System Qt](#system-qt) را ببینید). این مخزن همهٔ نسخه‌های پایدار را دریافت می‌کند، اما نسخه‌های بتا را نه، و امضا شده است. بسته‌های system-Qt به Debian 12، Ubuntu 22.04 یا جدیدتر نیاز دارند. در سیستم‌های قدیمی‌تر، به‌جای آن `.deb` معمولی را از [دانلود](@/downloads.fa.md) نصب کنید. برای افزودن این مخزن در Debian، Ubuntu و دیگر سیستم‌های مبتنی بر Debian:

```bash
sudo install -d -m 0755 /etc/apt/keyrings
sudo curl -fsSLo /etc/apt/keyrings/throne.asc https://throneproj.github.io/Throne/throne.asc
sudo curl -fsSLo /etc/apt/sources.list.d/throne.list https://throneproj.github.io/Throne/apt/throne.list
sudo apt update
sudo apt install throne
```

پس از آن، `sudo apt upgrade` هم Throne را به‌روز می‌کند.

اگر پیش‌تر یک `.deb` معمولی را از [دانلود](@/downloads.fa.md) نصب کرده‌اید، اولین نسخهٔ جدیدی که `apt upgrade` از این مخزن نصب می‌کند آن را با بستهٔ system-Qt جایگزین می‌کند، چون نام هر دو بسته `throne` است. برای نگه داشتن بستهٔ معمولی، مخزن را اضافه نکنید و نسخه‌های جدید را از [دانلود](@/downloads.fa.md) نصب کنید.

### مخزن RPM {#rpm-repository}

مخزن RPM در `throneproj.github.io/Throne/rpm` بسته‌های system-Qt را برای x86_64 و aarch64 دارد؛ بنابراین توزیع شما باید Qt 6.5 یا جدیدتر را فراهم کند. این بسته‌ها به glibc 2.34 یا جدیدتر هم نیاز دارند، پس روی RHEL 8 و نسخه‌های بازسازی‌شدهٔ آن نصب نمی‌شوند؛ آنجا `.rpm` معمولی را از [دانلود](@/downloads.fa.md) نصب کنید. این مخزن همهٔ نسخه‌های پایدار را دریافت می‌کند، اما نسخه‌های بتا را نه، و هم بسته‌ها و هم خود مخزن امضا شده‌اند.

Fedora و RHEL 9 یا جدیدتر:

```bash
sudo curl -fsSLo /etc/yum.repos.d/throne.repo https://throneproj.github.io/Throne/rpm/throne.repo
sudo dnf install throne --refresh
```

openSUSE و SLES:

```bash
sudo zypper addrepo -fc https://throneproj.github.io/Throne/rpm/throne.repo
sudo zypper install throne
```

بار اول، `dnf` و `zypper` از شما می‌خواهند که به کلید امضای مخزن اعتماد کنید.

اگر پیش‌تر یک `.rpm` معمولی را از [دانلود](@/downloads.fa.md) نصب کرده‌اید، اولین نسخهٔ جدیدی که `dnf upgrade` یا `zypper update` از این مخزن نصب می‌کند آن را با بستهٔ system-Qt جایگزین می‌کند، چون نام هر دو بسته `throne` است. برای نگه داشتن بستهٔ معمولی، مخزن را اضافه نکنید و نسخه‌های جدید را از [دانلود](@/downloads.fa.md) نصب کنید.

### بسته‌های System Qt {#system-qt}

Qt جعبه‌ابزار پنجرهٔ Throne است. بسته‌های معمولی نسخهٔ خودشان از Qt را همراه دارند. بسته‌های `-system-qt` به‌جای آن از کتابخانه‌های Qt 6 توزیع شما استفاده می‌کنند و مدیر بستهٔ شما آن‌ها را به‌عنوان وابستگی نصب می‌کند. وقتی بستهٔ معمولی اجرا نمی‌شود، برای مثال چون پردازندهٔ شما بیش از حد قدیمی است ([عیب‌یابی](@/get_started/installation.fa.md#troubleshooting) را ببینید)، یا وقتی می‌خواهید Throne از پوستهٔ Qt محیط دسکتاپ شما استفاده کند، از بستهٔ system-Qt استفاده کنید. [پرسش‌های متداول](@/help/faq.fa.md#deb-variants) را هم ببینید.

## مدیران بسته {#package-managers}

{% alert_warning() %}
بسته‌های WinGet، Scoop و Nix، و بسته‌های AUR به‌جز `throne`، را اعضای جامعهٔ کاربری ساخته‌اند، نه توسعه‌دهندگان Throne. این بسته‌ها ممکن است از آخرین نسخهٔ منتشرشده قدیمی‌تر باشند و رفتارشان با بیلدهای رسمی فرق کند. مشکلات چنین بسته‌ای را به نگه‌دارندهٔ آن گزارش دهید. اگر مطمئن نیستید مشکل از Throne است یا از بسته، ابتدا یک بیلد رسمی را از صفحهٔ [دانلود](@/downloads.fa.md) امتحان کنید. [پرسش‌های متداول](@/help/faq.fa.md#third-party-packages) را هم ببینید.
{% end %}

### WinGet {#winget}

```powershell
winget install -e --id Throneproj.Throne
```

### Scoop {#scoop}

```powershell
scoop bucket add extras
scoop install extras/throne
```

### Homebrew {#homebrew}

```bash
brew install --cask throneproj/tap/throne
```

این cask از tap خودِ Throne در Homebrew می‌آید و گردش‌کار انتشار Throne آن را برای هر نسخهٔ پایدار به‌روز می‌کند، اما نسخه‌های بتا را نه. این cask نسخهٔ Apple Silicon یا Intel از `Throne.app` را در `/Applications` نصب می‌کند و به macOS 14.4 یا جدیدتر نیاز دارد. برای Mac با پردازندهٔ Intel و macOS 10.15 تا 14.3، از ZIP با نام `macoslegacy` در بخش [macOS](#macos) استفاده کنید. Throne توسط Apple تأیید (notarize) نشده است، بنابراین cask پس از نصب برنامه پرچم قرنطینه (quarantine) را حذف می‌کند و به دستور `xattr` نیازی ندارید.

فهرست اصلی caskهای Homebrew فقط برنامه‌هایی را می‌پذیرد که Apple آن‌ها را تأیید (notarize) کرده باشد، بنابراین `brew install --cask throne`، Throne را پیدا نمی‌کند. همیشه از نام کامل `throneproj/tap/throne` استفاده کنید.

Throne را با `brew upgrade --cask throne` به‌روزرسانی کنید. پس از به‌روزرسانی، Throne دوباره دسترسی‌های لازم برای حالت TUN را درخواست می‌کند. دستور `brew uninstall --zap --cask throne` داده‌های شما در `~/Library/Preferences/Throne` را هم حذف می‌کند.

برای Debian، Ubuntu، Fedora، RHEL و openSUSE، Throne [مخزن APT](#apt-repository) و [مخزن RPM](#rpm-repository) خودش را دارد.

### Arch Linux (AUR) {#aur}

Throne با نام `throne` در Arch User Repository موجود است. آن را با یک AUR helper نصب کنید، برای مثال `yay -S throne` یا `paru -S throne`.

بستهٔ `throne`، Throne را از سورس می‌سازد و آن را در `/usr/lib/throne` نصب می‌کند. اسکریپت ساخت آن در مخزن Throne نگهداری می‌شود و گردش‌کار انتشار Throne این بسته را برای هر نسخهٔ پایدار به‌روز می‌کند. Throne داده‌های شما را در `~/.config/Throne` نگه می‌دارد.

### NixOS {#nixos}

این را به پیکربندی NixOS خود اضافه کنید:

```nix
programs.throne = {
  enable = true;
  tunMode.enable = true; # optional, needed for TUN mode
};
```

### Nix {#nix}

در NixOS، نام کانال معمولاً `nixos` است:

```bash
nix-env -iA nixos.throne
```

در توزیع‌های دیگر، نام کانال معمولاً `nixpkgs` است، پس از `nixpkgs.throne` استفاده کنید. برای امتحان کردن Throne بدون نصب آن، `nix-shell -p throne` را اجرا کنید.

## ساخت از سورس {#build-from-source}

Throne دو بخش دارد که جداگانه ساخته می‌شوند: رابط کاربری (`Throne`، نوشته‌شده با C++ و Qt) و هسته (`ThroneCore`، نوشته‌شده با Go). رابط کاربری بدون هسته نمی‌تواند متصل شود، بنابراین هسته باید در کنار فایل اجرایی `Throne` باشد. مخزن هیچ Git submodule ندارد، بنابراین یک `git clone` معمولی کافی است.

| بخش | پیش‌نیازها |
|---|---|
| رابط کاربری | CMake 3.20 یا جدیدتر، یک کامپایلر C++20، Qt 6.2 یا جدیدتر (Widgets، Network، LinguistTools و در Linux، DBus)، هدرهای توسعهٔ X11 و xcb در Linux |
| هسته | Go 1.26 یا جدیدتر، `protoc`، `protoc-gen-go` و `protoc-gen-go-grpc`. CGO در Linux و macOS: در Linux زنجیرهٔ ابزار Chromium از cronet-go (خروجی NaïveProxy به آن نیاز دارد) و در macOS ابزارهای خط فرمان Xcode. |

نمونه برای Ubuntu 22.04 یا جدیدتر روی x64:

1. ابزارهای ساخت و Qt را نصب کنید:

   ```bash
   sudo apt update
   sudo apt install build-essential cmake ninja-build git curl protobuf-compiler libx11-dev \
     qt6-base-dev qt6-tools-dev qt6-tools-dev-tools qt6-l10n-tools libqt6svg6-dev \
     qt6-translations-l10n libglx-dev libgl1-mesa-dev
   ```

2. Go 1.26 یا جدیدتر را از [go.dev/dl](https://go.dev/dl/) نصب کنید. بسته‌های Go در بیشتر توزیع‌ها قدیمی‌ترند.
3. رابط کاربری را بسازید. فرایند ساخت به `srslist.h`، یعنی فهرست نام‌های مجموعه‌قوانین، در پوشهٔ `build` نیاز دارد:

   ```bash
   git clone https://github.com/throneproj/Throne.git ~/Throne
   cd ~/Throne
   mkdir build
   cd build
   curl -fLso srslist.h https://raw.githubusercontent.com/throneproj/routeprofiles/rule-set/srslist.h
   cmake -GNinja -DCMAKE_BUILD_TYPE=Release ..
   ninja
   ```

4. پلاگین‌های protobuf را با همان نسخه‌هایی که بیلدهای رسمی به کار می‌برند نصب کنید و پوشهٔ `bin` مربوط به Go را به `PATH` اضافه کنید:

   ```bash
   go install google.golang.org/protobuf/cmd/protoc-gen-go@v1.36.12
   go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@v1.6.2
   export PATH="$PATH:$(go env GOPATH)/bin"
   ```

5. زنجیرهٔ ابزار Chromium را دانلود کنید. `<commit>` را با همان commit از cronet-go جایگزین کنید که مرحلهٔ `Clone cronet-go` در `.github/workflows/build.yml` آن را checkout می‌کند:

   ```bash
   git clone https://github.com/throneproj/cronet-go.git ~/cronet-go
   cd ~/cronet-go
   git checkout <commit>
   git submodule update --init --recursive --depth=1
   go run ./cmd/build-naive --target=linux/amd64 download-toolchain
   eval "$(go run ./cmd/build-naive --target=linux/amd64 env --export)"
   ```

   آخرین دستور، `CC`، `CXX` و `CGO_LDFLAGS` را فقط برای ترمینال فعلی تنظیم می‌کند. مرحلهٔ بعد را در همین ترمینال اجرا کنید.

6. هسته را بسازید و آن را در کنار رابط کاربری کپی کنید. `build_go.sh` پیش از ساخت، پوشهٔ `DEST` را پاک می‌کند، پس یک پوشهٔ مخصوص به آن بدهید:

   ```bash
   cd ~/Throne
   GOOS=linux GOARCH=amd64 DEST="$PWD/deployment/linux-amd64" ./script/build_go.sh
   cp deployment/linux-amd64/ThroneCore build/
   ```

7. Throne را با `./build/Throne` اجرا کنید.

روی ARM64، از `--target=linux/arm64` و `GOARCH=arm64` استفاده کنید.

- **macOS:** گزینهٔ `-DNKR_PACKAGE_MACOS=1` را به دستور `cmake` اضافه کنید؛ با این کار `Throne.app` ساخته می‌شود. هسته را با `GOOS=darwin` بسازید و `ThroneCore` را در `Throne.app/Contents/MacOS/` کپی کنید.
- **Windows:** بیلدهای رسمی رابط کاربری را با MSVC و Ninja کامپایل می‌کنند و هسته را با `GOOS=windows` روی Linux کراس‌کامپایل می‌کنند (اسکریپت برای Windows، CGO را خاموش می‌کند). `ThroneCore.exe` و `libcronet.dll` را از پوشهٔ `DEST` در کنار `Throne.exe` کپی کنید.

مراحل دقیق بیلدهای رسمی در `.github/workflows/build.yml` در [مخزن Throne](https://github.com/throneproj/Throne) آمده است. بیلدهای legacy برای Windows 7 و macOS 10.15 هسته را با [نسخهٔ MetaCubeX از Go](https://github.com/MetaCubeX/go) 1.26 کامپایل می‌کنند، چون Go رسمی دیگر از این سیستم‌ها پشتیبانی نمی‌کند.

## به‌روزرسانی {#updating}

نسخه‌های ZIP و نصب پیش‌فرضِ مخصوص کاربر با نصب‌کنندهٔ Windows می‌توانند خودشان را به‌روزرسانی کنند: روی `برنامه` (Program) ← `بررسی به‌روزرسانی` (Check For Update) و سپس `به‌روزرسانی` (Update) کلیک کنید (پیش از 1.4.0: `Tools` → `Check For Update`). بقیهٔ نسخه‌ها را به همان روشی به‌روزرسانی کنید که نصب کرده‌اید: نصب‌کنندهٔ جدید را اجرا کنید، بستهٔ `.deb` یا `.rpm` جدید را نصب کنید، اسکریپت نصب را دوباره اجرا کنید، در macOS `Throne.app` را جایگزین کنید، یا از مدیر بستهٔ خود استفاده کنید. نمایه‌ها و تنظیمات شما باقی می‌مانند.

از نسخهٔ 1.4.0، تا وقتی یک [Throne بدون رابط گرافیکی](@/headless/_index.fa.md) از همان نصب اجرا می‌شود، `بررسی به‌روزرسانی` دکمهٔ `به‌روزرسانی` را نشان نمی‌دهد. ابتدا Throne بدون رابط گرافیکی را متوقف کنید.

برای جزئیات، نسخه‌های بتا و پشتیبان‌گیری، [پشتیبان‌گیری، به‌روزرسانی و مهاجرت](@/guides/backup.fa.md#updating) را ببینید. پس از به‌روزرسانی در Linux یا macOS، Throne ممکن است دسترسی‌هایی را که برای حالت TUN لازم دارد دوباره درخواست کند.

## عیب‌یابی {#troubleshooting}

- **هشدار آنتی‌ویروس.** برخی آنتی‌ویروس‌ها Throne یا `ThroneCore` را خطرناک تشخیص می‌دهند، بیشتر به این دلیل که به‌روزرسان فایل‌های برنامه را دانلود و جایگزین می‌کند. [پرسش‌های متداول](@/help/faq.fa.md#antivirus) را ببینید.
- **Throne اجرا نمی‌شود و پیام «Incompatible processor. This Qt build requires the following features: sse4.2 popcnt» (پردازنده ناسازگار است) را نشان می‌دهد.** پردازندهٔ شما برای Qt همراه برنامه بیش از حد قدیمی است. در Windows، از `windowslegacy64.zip` استفاده کنید. در Linux، از یک بستهٔ `-system-qt` استفاده کنید. [پرسش‌های متداول](@/help/faq.fa.md#old-systems) را هم ببینید.
- **Linux: Throne اعلام می‌کند که نتوانسته پلاگین پلتفرم Qt با نام «xcb» را بارگذاری کند.** اگر Throne از Qt سیستم شما استفاده می‌کند، `libxcb-cursor0` (Debian، Ubuntu) یا `xcb-util-cursor` (Fedora، RHEL) را نصب کنید. فایل ZIP و بسته‌های معمولی این کتابخانه را همراه دارند؛ اگر آن‌ها این خطا را نشان می‌دهند، از یک بستهٔ `-system-qt` استفاده کنید.
- **Linux با GNOME: آیکون سینی سیستم دیده نمی‌شود.** افزونهٔ AppIndicator را برای GNOME نصب کنید.
- **macOS: «Throne is damaged and can't be opened» (Throne آسیب دیده است و باز نمی‌شود).** پرچم قرنطینه هنوز فعال است. دستور `xattr` را از مراحل [macOS](@/get_started/installation.fa.md#macos) اجرا کنید.

برای مشکلات پس از نصب، [عیب‌یابی](@/help/troubleshooting.fa.md#startup) را ببینید.

+++
title = "نصب"
description = "Throne را روی Windows، macOS یا Linux نصب کنید، آن را به‌روز نگه دارید یا از سورس بسازید."
weight = 1
toc = true
+++

فایل‌ها را از صفحهٔ [دانلود](@/downloads.fa.md) دریافت کنید. برای Android، [نصب و ارتقا](@/android/installation.fa.md) را ببینید.

## Windows {#windows}

| رایانهٔ شما | فایل |
|---|---|
| هر رایانهٔ Windows، اگر مطمئن نیستید | `Throne-<version>-windows-universal-installer.exe` |
| Windows 10 1809 یا جدیدتر، x64 | `Throne-<version>-windows64.zip` |
| Windows روی ARM64 | `Throne-<version>-windows-arm64.zip` |
| Windows 7 SP1 تا Windows 10 1803 (x64)، یا پردازنده‌ای بدون SSE4.2 | `Throne-<version>-windowslegacy64.zip` |
| Windows 7 SP1 یا جدیدتر، 32 بیتی (x86) | `Throne-<version>-windows32.zip` |

### نصب‌کننده {#windows-installer}

نصب‌کنندهٔ universal بیلد مناسب رایانهٔ شما را انتخاب می‌کند و آن را بدون دسترسی مدیر (administrator) در `%LOCALAPPDATA%\Throne` نصب می‌کند. داده‌های شما در پوشهٔ `config` آن نگه داشته می‌شوند. اگر به‌جای آن Throne را برای همهٔ کاربران در `Program Files` نصب کنید، Throne داده‌های شما را در `%LOCALAPPDATA%\Throne\config` نگه می‌دارد و نمی‌تواند خودش را به‌روزرسانی کند.

برای حذف Throne، آن را از فهرست برنامه‌های نصب‌شده در Windows حذف کنید. برنامهٔ حذف می‌پرسد که آیا نمایه‌ها و تنظیمات شما هم پاک شوند یا نه.

### ZIP قابل حمل {#windows-zip}

فایل ZIP را در پوشه‌ای استخراج کنید که بتوانید در آن فایل بنویسید و `Throne.exe` را از پوشهٔ `Throne` اجرا کنید. داده‌های شما در پوشهٔ `config` در کنار آن ذخیره می‌شوند، بنابراین می‌توانید کل پوشه را جابه‌جا کنید یا از آن پشتیبان بگیرید.

برخلاف نصب‌کننده، نسخهٔ ZIP تا وقتی گزینهٔ `تنظیمات` (Settings) ← `تنظیمات پایه` (Basic Settings) ← `متداول` (Common) ← `Register throne:// links at startup` را تیک نزنید، [لینک‌های `throne://`](@/advanced/deeplinks.fa.md#receive) را باز نمی‌کند (فقط در حالت پیشرفته؛ [حالت ساده و پیشرفته](@/get_started/configuration.fa.md#interface-mode) را ببینید).

## macOS {#macos}

| Mac شما | فایل |
|---|---|
| Apple Silicon، macOS 14.4 یا جدیدتر | `Throne-<version>-macos-arm64.zip` |
| Intel، macOS 14.4 یا جدیدتر | `Throne-<version>-macos-amd64.zip` |
| Intel، macOS 10.15 تا 14.3 | `Throne-<version>-macoslegacy-amd64.zip` |

فایل ZIP را این‌گونه نصب کنید، یا از [Homebrew](#homebrew) استفاده کنید که این کار را برایتان انجام می‌دهد:

1. فایل ZIP را استخراج کنید و پیش از اینکه `Throne.app` را برای نخستین بار باز کنید، آن را به `/Applications` منتقل کنید. اگر برنامه در `~/Downloads` باشد، Throne ممکن است نتواند دسترسی‌های لازم برای حالت TUN را بگیرد.
2. Throne با گواهی Apple امضا نشده است، بنابراین پرچم قرنطینه (quarantine) را حذف کنید؛ وگرنه macOS می‌گوید Throne «is damaged and can't be opened» (آسیب دیده است و باز نمی‌شود):

   ```bash
   xattr -d com.apple.quarantine /Applications/Throne.app
   ```

3. Throne را باز کنید.

داده‌های شما در پوشهٔ کاربری‌تان نگه داشته می‌شوند، بنابراین می‌توانید `Throne.app` را بدون از دست دادن نمایه‌هایتان جایگزین کنید. حالت TUN به یک حساب مدیر نیاز دارد؛ [حالت TUN](@/guides/tun_mode.fa.md#privileges) را ببینید.

## Linux {#linux}

| فایل | کاربرد |
|---|---|
| [اسکریپت نصب](#linux-script) | هر توزیعی. بیلد ZIP را در `/opt/Throne` نصب می‌کند. |
| `Throne-<version>-linux-amd64.zip`، `…-linux-arm64.zip` | یک نسخهٔ قابل حمل در پوشه‌ای به انتخاب شما |
| `Throne-<version>-debian-amd64.deb`، `…-debian-arm64.deb` | Debian، Ubuntu، Linux Mint و دیگر سیستم‌های مبتنی بر Debian |
| `Throne-<version>-fedora-amd64.rpm`، `…-fedora-arm64.rpm` | Fedora و سیستم‌های مبتنی بر RHEL |
| فایل‌هایی که به `-system-qt.deb` یا `-system-qt.rpm` ختم می‌شوند | همان بسته‌ها، با Qt سیستم شما |
| [مخزن APT](#apt-repository) و [مخزن RPM](#rpm-repository) | بسته‌های system-Qt، که مدیر بستهٔ شما آن‌ها را به‌روز نگه می‌دارد |

فایل‌های ZIP، اسکریپت نصب و بسته‌های معمولی نسخهٔ خودشان از Qt را همراه دارند و به glibc 2.28 یا جدیدتر نیاز دارند: Debian 10، Ubuntu 20.04، RHEL 8 (و AlmaLinux یا Rocky Linux 8)، Raspberry Pi OS (64 بیتی) bullseye یا جدیدتر. بسته‌های `-system-qt` از Qt 6 توزیع شما استفاده می‌کنند و به glibc 2.34 یا جدیدتر نیاز دارند، برای نمونه Debian 12 یا Ubuntu 22.04. وقتی بستهٔ معمولی اجرا نمی‌شود، برای مثال روی یک پردازندهٔ قدیمی، یا وقتی می‌خواهید Throne از پوستهٔ Qt محیط دسکتاپ شما استفاده کند، از این بسته‌ها استفاده کنید.

{% alert_warning() %}
Throne را با `sudo` یا به‌عنوان root اجرا نکنید. برای حالت TUN، Throne فقط به هستهٔ خود دسترسی root می‌دهد و گذرواژهٔ شما را از طریق `pkexec` می‌پرسد. [حالت TUN](@/guides/tun_mode.fa.md#privileges) را ببینید.
{% end %}

### اسکریپت نصب {#linux-script}

این اسکریپت به Python 3 نیاز دارد:

```bash
curl -fsSL https://raw.githubusercontent.com/throneproj/Throne/dev/script/install_linux.py | sudo python3
```

`Install` و شاخه (`Stable` یا `Unstable`) را انتخاب کنید، یا `Uninstall` را. برای به‌روزرسانی یا حذف، همین دستور را دوباره اجرا کنید. حذف، تنظیمات شما را در `~/.config/Throne` نگه می‌دارد.

### ZIP قابل حمل {#linux-zip}

```bash
unzip Throne-x.x.x-linux-amd64.zip
cd Throne
./Throne
```

داده‌های شما در پوشهٔ `config` داخل `Throne` نگه داشته می‌شوند.

### Debian و Ubuntu (.deb) {#deb}

```bash
sudo apt install ./Throne-x.x.x-debian-amd64.deb
```

### Fedora و RHEL (.rpm) {#rpm}

```bash
sudo dnf install ./Throne-x.x.x-fedora-amd64.rpm
```

هر دو بسته Throne را در `/opt/Throne` نصب می‌کنند و داده‌های شما را در `~/.config/Throne` نگه می‌دارند. برای دریافت به‌روزرسانی‌ها از طریق مدیر بسته، به‌جای آن از یک مخزن استفاده کنید.

### مخزن APT {#apt-repository}

مخزن امضاشدهٔ APT بسته‌های system-Qt همهٔ نسخه‌های پایدار را برای amd64 و arm64 دارد. این مخزن به Debian 12، Ubuntu 22.04 یا جدیدتر نیاز دارد؛ در سیستم‌های قدیمی‌تر، از `.deb` معمولی استفاده کنید.

```bash
sudo install -d -m 0755 /etc/apt/keyrings
sudo curl -fsSLo /etc/apt/keyrings/throne.asc https://throneproj.github.io/Throne/throne.asc
sudo curl -fsSLo /etc/apt/sources.list.d/throne.list https://throneproj.github.io/Throne/apt/throne.list
sudo apt update
sudo apt install throne
```

پس از آن، `sudo apt upgrade` هم Throne را به‌روز می‌کند.

### مخزن RPM {#rpm-repository}

مخزن امضاشدهٔ RPM بسته‌های system-Qt همهٔ نسخه‌های پایدار را برای x86_64 و aarch64 دارد. این بسته‌ها به Qt 6.5 و glibc 2.34 یا جدیدتر نیاز دارند، بنابراین روی RHEL 8 و نسخه‌های بازسازی‌شدهٔ آن، از `.rpm` معمولی استفاده کنید.

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

بار اول، `dnf` و `zypper` از شما می‌خواهند که به کلید امضا اعتماد کنید.

{% alert_info() %}
اگر پیش‌تر یک `.deb` یا `.rpm` معمولی نصب کرده‌اید، اولین ارتقا از یک مخزن آن را با بستهٔ system-Qt جایگزین می‌کند، چون نام هر دو بسته `throne` است. برای نگه داشتن بستهٔ معمولی، مخزن را اضافه نکنید.
{% end %}

## مدیران بسته {#package-managers}

{% alert_warning() %}
بسته‌های WinGet، Scoop و Nix، و بسته‌های AUR به‌جز `throne`، را جامعهٔ کاربری ساخته است. این بسته‌ها ممکن است از آخرین نسخهٔ منتشرشده قدیمی‌تر باشند و رفتارشان با بیلدهای رسمی فرق کند. اگر چیزی کار نکرد، ابتدا یک بیلد رسمی را از صفحهٔ [دانلود](@/downloads.fa.md) امتحان کنید و مشکلات بسته را به نگه‌دارندهٔ آن گزارش دهید.
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

tap رسمی همهٔ نسخه‌های پایدار را دریافت می‌کند. این cask، `Throne.app` را در `/Applications` نصب می‌کند، پرچم قرنطینه را حذف می‌کند و به macOS 14.4 یا جدیدتر نیاز دارد؛ روی Macهای Intel قدیمی‌تر، از ZIP با نام `macoslegacy` استفاده کنید. همیشه از نام کامل استفاده کنید: `brew install --cask throne`، Throne را پیدا نمی‌کند.

Throne را با `brew upgrade --cask throne` به‌روزرسانی کنید؛ پس از آن، Throne دوباره دسترسی‌های لازم برای حالت TUN را درخواست می‌کند. دستور `brew uninstall --zap --cask throne` داده‌های شما در `~/Library/Preferences/Throne` را هم حذف می‌کند.

### Arch Linux (AUR) {#aur}

`throne` را با یک AUR helper نصب کنید، برای مثال `yay -S throne` یا `paru -S throne`. این بسته Throne را از سورس می‌سازد، آن را در `/usr/lib/throne` نصب می‌کند و همهٔ نسخه‌های پایدار را دریافت می‌کند. داده‌های شما در `~/.config/Throne` نگه داشته می‌شوند.

### Nix {#nix}

در NixOS، این را به پیکربندی خود اضافه کنید:

```nix
programs.throne = {
  enable = true;
  tunMode.enable = true; # optional, needed for TUN mode
};
```

یا آن را با `nix-env` نصب کنید. نام کانال در NixOS معمولاً `nixos` و در توزیع‌های دیگر `nixpkgs` است:

```bash
nix-env -iA nixos.throne
```

برای امتحان کردن Throne بدون نصب آن، `nix-shell -p throne` را اجرا کنید.

## ساخت از سورس {#build-from-source}

Throne دو بخش دارد: رابط کاربری (`Throne`، با C++ و Qt) و هسته (`ThroneCore`، با Go). هسته باید در کنار فایل اجرایی `Throne` باشد.

| بخش | پیش‌نیازها |
|---|---|
| رابط کاربری | CMake 3.20 یا جدیدتر، یک کامپایلر C++20، Qt 6.2 یا جدیدتر (Widgets، Network، LinguistTools و در Linux، DBus)، هدرهای توسعهٔ X11 و xcb در Linux |
| هسته | Go 1.26 یا جدیدتر، `protoc`، `protoc-gen-go` و `protoc-gen-go-grpc`. CGO: در Linux زنجیرهٔ ابزار Chromium از cronet-go (برای NaïveProxy) و در macOS ابزارهای خط فرمان Xcode. |

رابط کاربری را بسازید. فرایند ساخت به `srslist.h`، یعنی فهرست نام‌های مجموعه‌قوانین، در پوشهٔ `build` نیاز دارد:

```bash
git clone https://github.com/throneproj/Throne.git ~/Throne
cd ~/Throne
mkdir build
cd build
curl -fLso srslist.h https://raw.githubusercontent.com/throneproj/routeprofiles/rule-set/srslist.h
cmake -GNinja -DCMAKE_BUILD_TYPE=Release ..
ninja
```

سپس هسته را با `script/build_go.sh` بسازید و آن را در کنار رابط کاربری کپی کنید. در Linux، ابتدا زنجیرهٔ ابزار cronet-go را در همین ترمینال آماده کنید. `build_go.sh` پیش از ساخت، پوشهٔ `DEST` را پاک می‌کند، پس یک پوشهٔ مخصوص به آن بدهید:

```bash
cd ~/Throne
GOOS=linux GOARCH=amd64 DEST="$PWD/deployment/linux-amd64" ./script/build_go.sh
cp deployment/linux-amd64/ThroneCore build/
```

در macOS، گزینهٔ `-DNKR_PACKAGE_MACOS=1` را به `cmake` اضافه کنید، هسته را با `GOOS=darwin` بسازید و `ThroneCore` را در `Throne.app/Contents/MacOS/` کپی کنید. مراحل دقیق برای همهٔ پلتفرم‌ها، از جمله زنجیرهٔ ابزار cronet-go، نسخه‌های پلاگین‌های protobuf، Windows و بیلدهای legacy، در `.github/workflows/build.yml` در [مخزن Throne](https://github.com/throneproj/Throne) آمده است.

## به‌روزرسانی {#updating}

نسخه‌های ZIP برای Windows و Linux و نصب مخصوص کاربر با نصب‌کنندهٔ Windows خودشان را به‌روزرسانی می‌کنند: `برنامه` (Program) ← `بررسی به‌روزرسانی` (Check For Update) و سپس `به‌روزرسانی` (Update). اگر یک [Throne بدون رابط گرافیکی](@/headless/_index.fa.md) از همان نصب اجرا می‌شود، ابتدا آن را متوقف کنید. بقیهٔ نسخه‌ها را به همان روشی به‌روزرسانی کنید که نصب کرده‌اید: نصب‌کنندهٔ جدید را اجرا کنید، بستهٔ جدید را نصب کنید، اسکریپت نصب را دوباره اجرا کنید، در macOS `Throne.app` را جایگزین کنید، یا از مدیر بستهٔ خود استفاده کنید. نمایه‌ها و تنظیمات شما باقی می‌مانند.

پس از به‌روزرسانی در Linux یا macOS، Throne ممکن است دسترسی‌هایی را که برای حالت TUN لازم دارد دوباره درخواست کند. برای نسخه‌های بتا و پشتیبان‌گیری، [پشتیبان‌گیری، به‌روزرسانی و مهاجرت](@/guides/backup.fa.md#updating) را ببینید.

## عیب‌یابی {#troubleshooting}

اگر Throne پس از نصب اجرا نمی‌شود یا خطایی نشان می‌دهد، [مشکلات اجرا و محیط دسکتاپ](@/help/troubleshooting.fa.md#startup) را ببینید. اگر آنتی‌ویروس شما Throne را خطرناک تشخیص می‌دهد، [پرسش‌های متداول](@/help/faq.fa.md#antivirus) را ببینید.

+++
title = "مهاجرت از Nekoray / NekoBox"
description = "قابلیت‌های Nekoray و NekoBox در کجای Throne قرار دارند و چگونه داده‌های خود را منتقل کنید."
weight = 40
toc = true
+++

## هر چیز کجا رفته است؟ {#where-did-it-go}

| در Nekoray / NekoBox از این استفاده می‌کردید… | در Throne از این استفاده می‌کنید… |
| --- | --- |
| منوی `Server` | روی فهرست نمایه‌ها راست‌کلیک کنید. |
| منوی `Preferences` | دکمهٔ `تنظیمات` (Settings) در نوار ابزار. |
| `Tun Settings` → `Bypass Process Name` | قوانین `processName:` در کادر `مستقیم` (Direct) نمایهٔ مسیریابی شما، برای مثال `processName:game.exe`. |
| `Tun Settings` → `Whitelist mode` | نمایهٔ مسیریابی‌ای که `Default outbound` آن روی `direct` تنظیم شده است، و برنامه‌ها یا سایت‌ها در کادر `پروکسی` (Proxy). |
| `Tun Settings` → `Bypass CIDR` | `تنظیمات vpn` (Tun Settings) ← `Private Range Bypass`، یا قوانین `ip:` در کادر `مستقیم`. |
| یک `Preset` مسیریابی برای کشورتان | `مسیریابی` (Routing) ← `Download Profiles`. |
| `Default Outbound` روی `bypass` | `Default outbound` روی `direct` در نمایهٔ مسیریابی. |
| پوسته‌هایی مانند FlatGray، LightBlue و BlackSoft | `تنظیمات پایه` (Basic Settings) ← `استایل` (Style) ← `پوسته` (Theme). |
| `Copy links of selected (Neko Links)` | لینک‌های اشتراک‌گذاری استاندارد. Throne نمی‌تواند لینک‌های `nekoray://` یا `sn://` را بخواند. |
| `ربایش` (Hijack) یا `دی‌ان‌اس سیستم` (System DNS) (Throne 1.3.2 و قدیمی‌تر) | `حالتvpn` (Tun Mode). |
| NekoBox for Android | Throne برای Android؛ [پایین‌تر](#from-nekobox-android) را ببینید. |

حالت ساده برخی از این موارد را پنهان می‌کند؛ [حالت ساده و پیشرفته](@/get_started/configuration.fa.md#interface-mode) را ببینید. برای مسیریابی به‌تفکیک برنامه، `حالتvpn` (Tun Mode) را روشن کنید: برنامه‌هایی که پروکسی سیستمی را نادیده می‌گیرند هرگز به Throne نمی‌رسند. [مسیریابی](@/guides/routing.fa.md#simple-rules) را ببینید.

## انتقال داده‌ها {#moving-data}

### از Nekoray {#from-nekoray}

Throne نمی‌تواند پیکربندی Nekoray را بخواند. داده‌های خود را دستی منتقل کنید:

1. در Nekoray، نشانی (URL) هر اشتراک را از تنظیمات گروه آن کپی کنید. در Throne، `Ctrl+V` را فشار دهید و `ایجاد گروه اشتراک جدید` (Create new subscription group) را انتخاب کنید.
2. سرورهایی را که دستی اضافه کرده‌اید در Nekoray انتخاب کنید، از `Server` → `Share` → `Copy links of selected` استفاده کنید و در Throne `Ctrl+V` را فشار دهید.
3. مسیریابی ([مسیریابی](@/guides/routing.fa.md)) و تنظیماتی را که تغییر داده بودید، مانند پورت شنود، سرورهای DNS و کلیدهای میانبر، دوباره تنظیم کنید.

Nekoray و Throne را هم‌زمان و با حالت TUN یا پروکسی سیستمی روشن اجرا نکنید.

### از Throne 1.0.x {#from-1-0}

Throne 1.1.0 از فایل‌های JSON به سبک Nekoray به یک پایگاه‌داده (`throne.db`) منتقل شد و نمی‌تواند فایل‌های قدیمی را بخواند: پس از به‌روزرسانی درجای نسخهٔ 1.0.x، نمایه‌ها، گروه‌ها و تنظیمات شما دیده نمی‌شوند ([#1765](https://github.com/throneproj/Throne/issues/1765)). از پوشهٔ قدیمی یک کپی نگه دارید و داده‌های خود را همان‌طور که در بالا توضیح داده شد، دستی منتقل کنید؛ [#1202](https://github.com/throneproj/Throne/issues/1202) مراحل دستی و یک اسکریپت غیررسمی ساختهٔ جامعهٔ کاربری را فهرست می‌کند. بین نسخه‌های جدیدتر، از `تنظیمات پایه` (Basic Settings) ← `Backup and Restore` استفاده کنید ([پشتیبان‌گیری، به‌روزرسانی و مهاجرت](@/guides/backup.fa.md#backup-restore)).

### از NekoBox for Android {#from-nekobox-android}

Throne برای Android 2.0.0، که پیش‌تر NekoBox for Android نام داشت، از نو بر پایهٔ هستهٔ نسخهٔ دسکتاپ ساخته شده است و نمی‌تواند نسخهٔ قدیمی‌تر را به‌روزرسانی کند:

- با کلید جدیدی امضا شده است، بنابراین ابتدا نسخهٔ قدیمی را حذف کنید.
- فقط فایل‌های `.thrbackup` را بازیابی می‌کند، از جمله پشتیبان‌های دسکتاپ، نه فایل‌های پشتیبان یا فایل‌های `.zip` در WebDAV از نسخهٔ 1.6.x و قدیمی‌تر.

پیش از حذف، نشانی‌های اشتراک خود و لینک‌های اشتراک‌گذاری سرورهایی را که دستی اضافه کرده‌اید کپی کنید، یا به‌جای آن یک پشتیبان دسکتاپ را روی گوشی بازیابی کنید ([پشتیبان‌گیری، به‌روزرسانی و مهاجرت](@/guides/backup.fa.md#desktop-android)). جزئیات: [نصب و ارتقا](@/android/installation.fa.md#upgrading).

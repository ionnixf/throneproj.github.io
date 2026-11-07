+++
title = "Установка"
description = "Установка Throne в Windows, macOS или Linux, обновление до новых версий и сборка из исходного кода."
weight = 1
toc = true
+++

Скачать файлы можно на [странице загрузок](@/downloads.ru.md). Об установке на Android см. [Установка и обновление](@/android/installation.ru.md).

## Windows {#windows}

| Ваш компьютер | Файл |
|---|---|
| Любой компьютер с Windows, если вы не уверены | `Throne-<version>-windows-universal-installer.exe` |
| Windows 10 1809 или новее, x64 | `Throne-<version>-windows64.zip` |
| Windows на ARM64 | `Throne-<version>-windows-arm64.zip` |
| От Windows 7 SP1 до Windows 10 1803 (x64) или процессор без SSE4.2 | `Throne-<version>-windowslegacy64.zip` |
| 32-битная (x86) Windows 7 SP1 или новее | `Throne-<version>-windows32.zip` |

### Установщик {#windows-installer}

Универсальный установщик сам выбирает подходящую для вашего компьютера сборку и устанавливает её в `%LOCALAPPDATA%\Throne`, без прав администратора. Ваши данные хранятся в её папке `config`. Если вместо этого установить Throne для всех пользователей в `Program Files`, Throne хранит ваши данные в `%LOCALAPPDATA%\Throne\config` и не может обновляться сам.

Чтобы удалить Throne, удалите его из списка установленных приложений Windows. Программа удаления спросит, удалить ли заодно ваши профили и настройки.

### Портативный ZIP {#windows-zip}

Распакуйте ZIP-архив в папку, в которую у вас есть права на запись, и запустите `Throne.exe` из папки `Throne`. Ваши данные хранятся в папке `config` рядом с ним, поэтому всю папку можно перемещать или копировать целиком как резервную копию.

В отличие от установщика, копия из ZIP-архива открывает [ссылки `throne://`](@/advanced/deeplinks.ru.md#receive) только после того, как вы отметите `Настройки` (Settings) → `Основные настройки` (Basic Settings) → `Общие` (Common) → `Регистрировать обработчик ссылок throne:// при запуске` (Register throne:// links at startup) (только в расширенном режиме, см. [Простой и расширенный режим](@/get_started/configuration.ru.md#interface-mode)).

## macOS {#macos}

| Ваш Mac | Файл |
|---|---|
| Apple Silicon, macOS 14.4 или новее | `Throne-<version>-macos-arm64.zip` |
| Intel, macOS 14.4 или новее | `Throne-<version>-macos-amd64.zip` |
| Intel, macOS с 10.15 по 14.3 | `Throne-<version>-macoslegacy-amd64.zip` |

Установите ZIP-архив так, или используйте [Homebrew](#homebrew), который сделает это за вас:

1. Распакуйте ZIP-архив и переместите `Throne.app` в `/Applications` до первого запуска. Из `~/Downloads` Throne может не получить права, нужные для режима TUN.
2. Throne не подписан Apple, поэтому снимите флаг карантина, иначе macOS сообщит, что Throne «is damaged and can't be opened» (повреждён, и его не удаётся открыть):

   ```bash
   xattr -d com.apple.quarantine /Applications/Throne.app
   ```

3. Откройте Throne.

Ваши данные хранятся в папке пользователя, поэтому `Throne.app` можно заменить без потери профилей. Для режима TUN нужна учётная запись администратора; см. [Режим TUN](@/guides/tun_mode.ru.md#privileges).

## Linux {#linux}

| Файл | Для чего подходит |
|---|---|
| [Установочный скрипт](#linux-script) | Любой дистрибутив. Устанавливает ZIP-сборку в `/opt/Throne`. |
| `Throne-<version>-linux-amd64.zip`, `…-linux-arm64.zip` | Портативная копия в папке на ваш выбор |
| `Throne-<version>-debian-amd64.deb`, `…-debian-arm64.deb` | Debian, Ubuntu, Linux Mint и другие системы на основе Debian |
| `Throne-<version>-fedora-amd64.rpm`, `…-fedora-arm64.rpm` | Fedora и системы на основе RHEL |
| Файлы, имена которых заканчиваются на `-system-qt.deb` или `-system-qt.rpm` | Те же пакеты, но с Qt вашей системы |
| [APT-репозиторий](#apt-repository) и [RPM-репозиторий](#rpm-repository) | Пакеты с системным Qt, которые поддерживает в актуальном состоянии ваш менеджер пакетов |

ZIP-файлы, установочный скрипт и обычные пакеты содержат собственную копию Qt, и им нужна glibc 2.28 или новее: Debian 10, Ubuntu 20.04, RHEL 8 (а также AlmaLinux и Rocky Linux 8), Raspberry Pi OS (64-bit) bullseye или новее. Пакеты `-system-qt` используют Qt 6 вашего дистрибутива, и им нужна glibc 2.34 или новее, например в Debian 12 или Ubuntu 22.04. Используйте их, если обычный пакет не запускается, например на старом процессоре, или если вы хотите, чтобы Throne использовал тему Qt вашего рабочего стола.

{% alert_warning() %}
Не запускайте Throne через `sudo` или от имени root. Для режима TUN Throne выдаёт права root только своему ядру и запрашивает ваш пароль через `pkexec`. См. [Режим TUN](@/guides/tun_mode.ru.md#privileges).
{% end %}

### Установочный скрипт {#linux-script}

Скрипту нужен Python 3:

```bash
curl -fsSL https://raw.githubusercontent.com/throneproj/Throne/dev/script/install_linux.py | sudo python3
```

Выберите `Install` и ветку (`Stable` или `Unstable`) либо `Uninstall`. Чтобы обновить или удалить Throne, запустите ту же команду ещё раз. При удалении ваши настройки в `~/.config/Throne` сохраняются.

### Портативный ZIP {#linux-zip}

```bash
unzip Throne-x.x.x-linux-amd64.zip
cd Throne
./Throne
```

Ваши данные хранятся в папке `config` внутри `Throne`.

### Debian и Ubuntu (.deb) {#deb}

```bash
sudo apt install ./Throne-x.x.x-debian-amd64.deb
```

### Fedora и RHEL (.rpm) {#rpm}

```bash
sudo dnf install ./Throne-x.x.x-fedora-amd64.rpm
```

Оба пакета устанавливают Throne в `/opt/Throne` и хранят ваши данные в `~/.config/Throne`. Чтобы получать обновления через менеджер пакетов, используйте вместо этого репозиторий.

### APT-репозиторий {#apt-repository}

Подписанный APT-репозиторий содержит пакеты с системным Qt для каждого стабильного релиза, для amd64 и arm64. Ему нужен Debian 12, Ubuntu 22.04 или новее; в более старых системах используйте обычный `.deb`.

```bash
sudo install -d -m 0755 /etc/apt/keyrings
sudo curl -fsSLo /etc/apt/keyrings/throne.asc https://throneproj.github.io/Throne/throne.asc
sudo curl -fsSLo /etc/apt/sources.list.d/throne.list https://throneproj.github.io/Throne/apt/throne.list
sudo apt update
sudo apt install throne
```

После этого `sudo apt upgrade` обновляет и Throne.

### RPM-репозиторий {#rpm-repository}

Подписанный RPM-репозиторий содержит пакеты с системным Qt для каждого стабильного релиза, для x86_64 и aarch64. Им нужны Qt 6.5 и glibc 2.34 или новее, поэтому в RHEL 8 и его пересборках используйте обычный `.rpm`.

Fedora и RHEL 9 или новее:

```bash
sudo curl -fsSLo /etc/yum.repos.d/throne.repo https://throneproj.github.io/Throne/rpm/throne.repo
sudo dnf install throne --refresh
```

openSUSE и SLES:

```bash
sudo zypper addrepo -fc https://throneproj.github.io/Throne/rpm/throne.repo
sudo zypper install throne
```

При первом использовании `dnf` и `zypper` просят подтвердить доверие к ключу подписи.

{% alert_info() %}
Если раньше вы установили обычный `.deb` или `.rpm`, первое обновление из репозитория заменит его пакетом с системным Qt, потому что оба пакета называются `throne`. Чтобы оставить обычный пакет, не добавляйте репозиторий.
{% end %}

## Менеджеры пакетов {#package-managers}

{% alert_warning() %}
Пакеты WinGet, Scoop и Nix, а также пакеты AUR, кроме `throne`, создаёт сообщество. Они могут отставать от последнего релиза и работать не так, как официальные сборки. Если что-то не работает, сначала проверьте официальную сборку со [страницы загрузок](@/downloads.ru.md), а о проблемах с пакетом сообщайте его сопровождающему.
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

В официальный tap попадает каждый стабильный релиз. Cask устанавливает `Throne.app` в `/Applications`, снимает флаг карантина и требует macOS 14.4 или новее; на старых Mac с Intel используйте ZIP-архив `macoslegacy`. Всегда указывайте полное имя: `brew install --cask throne` не находит Throne.

Обновляйте Throne командой `brew upgrade --cask throne`; после этого Throne снова запрашивает права, нужные для режима TUN. `brew uninstall --zap --cask throne` также удаляет ваши данные в `~/Library/Preferences/Throne`.

### Arch Linux (AUR) {#aur}

Установите `throne` с помощью AUR-помощника, например `yay -S throne` или `paru -S throne`. Пакет собирает Throne из исходного кода, устанавливает его в `/usr/lib/throne` и обновляется с каждым стабильным релизом. Ваши данные хранятся в `~/.config/Throne`.

### Nix {#nix}

В NixOS добавьте это в конфигурацию:

```nix
programs.throne = {
  enable = true;
  tunMode.enable = true; # optional, needed for TUN mode
};
```

Или установите его через `nix-env`. В NixOS канал обычно называется `nixos`, в других дистрибутивах — `nixpkgs`:

```bash
nix-env -iA nixos.throne
```

Чтобы попробовать Throne без установки, выполните `nix-shell -p throne`.

## Сборка из исходного кода {#build-from-source}

Throne состоит из двух частей: интерфейса (`Throne`, C++ и Qt) и ядра (`ThroneCore`, Go). Ядро должно лежать рядом с исполняемым файлом `Throne`.

| Часть | Требования |
|---|---|
| Интерфейс | CMake 3.20 или новее, компилятор C++20, Qt 6.2 или новее (Widgets, Network, LinguistTools, а в Linux также DBus), заголовочные файлы X11 и xcb для разработки в Linux |
| Ядро | Go 1.26 или новее, `protoc`, `protoc-gen-go` и `protoc-gen-go-grpc`. CGO: в Linux — набор инструментов Chromium из cronet-go (для NaïveProxy), в macOS — инструменты командной строки Xcode. |

Соберите интерфейс. Для сборки в папке `build` нужен файл `srslist.h` — список имён наборов правил:

```bash
git clone https://github.com/throneproj/Throne.git ~/Throne
cd ~/Throne
mkdir build
cd build
curl -fLso srslist.h https://raw.githubusercontent.com/throneproj/routeprofiles/rule-set/srslist.h
cmake -GNinja -DCMAKE_BUILD_TYPE=Release ..
ninja
```

Затем соберите ядро скриптом `script/build_go.sh` и скопируйте его в папку с интерфейсом. В Linux сначала настройте набор инструментов cronet-go в том же терминале. `build_go.sh` удаляет папку `DEST` перед сборкой, поэтому укажите для неё отдельную папку:

```bash
cd ~/Throne
GOOS=linux GOARCH=amd64 DEST="$PWD/deployment/linux-amd64" ./script/build_go.sh
cp deployment/linux-amd64/ThroneCore build/
```

В macOS добавьте `-DNKR_PACKAGE_MACOS=1` к команде `cmake`, соберите ядро с `GOOS=darwin` и скопируйте `ThroneCore` в `Throne.app/Contents/MacOS/`. Точные шаги для всех платформ, включая набор инструментов cronet-go, версии плагинов protobuf, Windows и legacy-сборки, описаны в `.github/workflows/build.yml` в [репозитории Throne](https://github.com/throneproj/Throne).

## Обновление {#updating}

Копии для Windows и Linux из ZIP-архива и установка через установщик Windows для текущего пользователя обновляются сами: `Программа` (Program) → `Проверить наличие обновлений` (Check For Update), затем `Обновить` (Update). Если из той же установки работает [headless Throne](@/headless/_index.ru.md), сначала остановите его. Все остальные копии обновляйте тем же способом, которым устанавливали: запустите новый установщик, установите новый пакет, снова запустите установочный скрипт, замените `Throne.app` в macOS или воспользуйтесь менеджером пакетов. Ваши профили и настройки сохраняются.

После обновления в Linux или macOS Throne может снова запросить права, необходимые для режима TUN. Бета-версии и резервное копирование описаны на странице [Резервное копирование, обновление и перенос](@/guides/backup.ru.md#updating).

## Устранение неполадок {#troubleshooting}

Если после установки Throne не запускается или показывает ошибку, см. [Проблемы с запуском и рабочим окружением](@/help/troubleshooting.ru.md#startup). Если антивирус помечает Throne, см. [FAQ](@/help/faq.ru.md#antivirus).

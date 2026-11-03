+++
title = "Установка"
description = "Установка Throne в Windows, macOS или Linux, обновление до новых версий и сборка из исходного кода."
weight = 1
toc = true
+++

На этой странице описано, как установить Throne в Windows, macOS и Linux, как его обновлять и как собрать из исходного кода. Скачать файлы можно на [странице загрузок](@/downloads.ru.md). Об установке на Android см. [Установка и обновление](@/android/installation.ru.md).

## Windows {#windows}

### Какой файл скачать {#windows-files}

| Ваш компьютер | Файл |
|---|---|
| Любой компьютер с Windows, если вы не уверены | `Throne-<version>-windows-universal-installer.exe` |
| Windows 10 1809 или новее, x64 | `Throne-<version>-windows64.zip` |
| Windows на ARM64 | `Throne-<version>-windows-arm64.zip` |
| От Windows 7 SP1 до Windows 10 1803 (x64) или процессор без SSE4.2 | `Throne-<version>-windowslegacy64.zip` |
| 32-битная (x86) Windows 7 SP1 или новее | `Throne-<version>-windows32.zip` |

### Установщик {#windows-installer}

Универсальный установщик — самый простой вариант:

- Он устанавливает Throne для вашей учётной записи в `%LOCALAPPDATA%\Throne`. Права администратора не нужны.
- Он сам выбирает подходящую для вашего компьютера сборку: x64, ARM64 или 32-битную. В x64-версиях Windows, выпущенных до Windows 10 1809, он устанавливает legacy-сборку.
- Он добавляет Throne в меню «Пуск» и на рабочий стол.
- Установленный Throne при каждом запуске регистрирует ссылки `throne://`, поэтому такие ссылки открываются в Throne. См. [Глубокие ссылки](@/advanced/deeplinks.ru.md#receive).

Throne хранит ваши профили и настройки в папке `config` внутри папки установки. Если вы устанавливали Throne старым установщиком (до 1.3.0), новый установщик использует ту же папку и сохраняет ваши данные.

Программа установки может также установить Throne для всех пользователей — в `Program Files`. Для этого нужны права администратора. Throne не может записывать в `Program Files`, поэтому хранит ваши данные в `%LOCALAPPDATA%\Throne\config`, а обновление в один щелчок недоступно.

Чтобы удалить Throne, удалите его из списка установленных приложений Windows. Программа удаления спросит, удалить ли заодно ваши профили, настройки и логи. Она также удаляет регистрацию ссылок `throne://`.

### Портативный ZIP {#windows-zip}

1. Скачайте ZIP-архив для вашей системы (см. таблицу выше).
2. Распакуйте его в папку, в которую у вас есть права на запись, например в папку внутри вашего профиля пользователя.
3. Откройте папку `Throne` и запустите `Throne.exe`.

Храните все файлы этой папки вместе. Throne сохраняет ваши данные в папке `config` рядом с `Throne.exe`, поэтому всю папку можно перемещать или копировать целиком как резервную копию. Если Throne не может записывать в свою папку, он использует `%LOCALAPPDATA%\Throne\config`.

Копии из ZIP-архива по умолчанию не регистрируют ссылки `throne://`. Чтобы включить регистрацию, отметьте `Настройки` (Settings) → `Основные настройки` (Basic Settings) → `Общие` (Common) → `Регистрировать обработчик ссылок throne:// при запуске` (Register throne:// links at startup) (только в расширенном режиме, см. [Простой и расширенный режим](@/get_started/configuration.ru.md#interface-mode)).

## macOS {#macos}

| Ваш Mac | Файл |
|---|---|
| Apple Silicon, macOS 14.4 или новее | `Throne-<version>-macos-arm64.zip` |
| Intel, macOS 14.4 или новее | `Throne-<version>-macos-amd64.zip` |
| Intel, macOS с 10.15 по 14.3 | `Throne-<version>-macoslegacy-amd64.zip` |

Начиная с 1.4.0 обычным сборкам нужна macOS 14.4 или новее; в Throne 1.3 и более старых версиях им была нужна macOS 13 или новее.

Throne также можно установить через [Homebrew](#homebrew): он выполняет шаги ниже за вас и обновляет Throne командой `brew upgrade`.

1. Скачайте ZIP-архив и распакуйте его. Внутри находится папка `Throne` с `Throne.app`.
2. Переместите `Throne.app` в `/Applications` до первого запуска. Для режима TUN Throne выдаёт своему ядру права root через Терминал, и это может не сработать, пока приложение находится в `~/Downloads`.
3. Throne не подписан сертификатом Apple, поэтому снимите с него флаг карантина в Терминале. Без этого шага macOS сообщает, что Throne «is damaged and can't be opened» (повреждён, и его не удаётся открыть).

   ```bash
   xattr -d com.apple.quarantine /Applications/Throne.app
   ```

4. Откройте Throne.

Throne хранит ваши данные в папке пользователя, а не внутри `Throne.app`, поэтому приложение можно заменить без потери профилей. Эту папку показывает пункт `Настройки` (Settings) → `Открыть папку конфигурации` (Open Config Folder) (только в расширенном режиме). Для режима TUN нужна учётная запись администратора; см. [Режим TUN](@/guides/tun_mode.ru.md#privileges).

## Linux {#linux}

| Файл | Для чего подходит |
|---|---|
| Установочный скрипт (см. ниже) | Любой дистрибутив. Устанавливает ZIP-сборку в `/opt/Throne`. |
| `Throne-<version>-linux-amd64.zip`, `…-linux-arm64.zip` | Портативная копия в папке на ваш выбор |
| `Throne-<version>-debian-amd64.deb`, `…-debian-arm64.deb` | Debian, Ubuntu, Linux Mint и другие системы на основе Debian |
| `Throne-<version>-fedora-amd64.rpm`, `…-fedora-arm64.rpm` | Fedora и системы на основе RHEL (начиная с 1.3.0) |
| Файлы, имена которых заканчиваются на `-system-qt.deb` или `-system-qt.rpm` | Те же пакеты, но они используют библиотеки Qt вашей системы |
| [APT-репозиторий](#apt-repository) и [RPM-репозиторий](#rpm-repository) | Пакеты с системным Qt, которые поддерживает в актуальном состоянии ваш менеджер пакетов |

Начиная с версии 1.4.0, ZIP-файлам, установочному скрипту и обычным пакетам `.deb` и `.rpm` нужна glibc 2.28 или новее. Они работают в Debian 10, Ubuntu 20.04, RHEL 8 (а также AlmaLinux и Rocky Linux 8), Raspberry Pi OS (64-bit) bullseye и более новых системах. Пакетам `-system-qt` нужна glibc 2.34 или новее и Qt 6 вашего дистрибутива, например в Debian 12 или Ubuntu 22.04. До версии 1.4.0 всем сборкам для Linux нужна была glibc 2.34 или новее, а файлам для ARM64 со встроенным Qt — glibc 2.38 или новее.

{% alert_warning() %}
Не запускайте Throne через `sudo` или от имени root. Для режима TUN Throne выдаёт права root только своему ядру, `ThroneCore`, и запрашивает ваш пароль через `pkexec`. См. [Режим TUN](@/guides/tun_mode.ru.md#privileges).
{% end %}

### Установочный скрипт {#linux-script}

Скрипт скачивает сборку для вашего процессора, устанавливает её в `/opt/Throne` и добавляет Throne в меню приложений. Ему нужны Python 3 и права root:

```bash
curl -fsSL https://raw.githubusercontent.com/throneproj/Throne/dev/script/install_linux.py | sudo python3
```

Скрипт показывает последние стабильную и нестабильную версии, а также установленную версию, если она есть. Выберите `Install` или `Uninstall`. Для `Install` выберите также ветку: `Stable` или `Unstable`.

Чтобы обновить или удалить Throne, запустите ту же команду ещё раз. При удалении стираются `/opt/Throne` и пункт меню, но ваши настройки в `~/.config/Throne` сохраняются.

### Портативный ZIP {#linux-zip}

ZIP-архив содержит папку `Throne`. Throne хранит ваши данные в папке `config` внутри неё.

```bash
unzip Throne-x.x.x-linux-amd64.zip
cd Throne
./Throne
```

### Debian и Ubuntu (.deb) {#deb}

```bash
sudo apt install ./Throne-x.x.x-debian-amd64.deb
```

Чтобы получать новые версии через `apt upgrade`, используйте вместо этого [APT-репозиторий](#apt-repository).

### Fedora и RHEL (.rpm) {#rpm}

```bash
sudo dnf install ./Throne-x.x.x-fedora-amd64.rpm
```

Чтобы получать новые версии через `dnf upgrade` или `zypper update`, используйте вместо этого [RPM-репозиторий](#rpm-repository).

Оба пакета устанавливают Throne в `/opt/Throne` и добавляют его в меню приложений. В этом случае Throne хранит ваши данные в `~/.config/Throne`.

Начиная с 1.4.0 пакеты также устанавливают службу systemd для [headless Throne](@/headless/_index.ru.md), `/usr/lib/systemd/system/throne.service`, и её файл настроек `/etc/throne/throne.env`. Пакет никогда не включает и не запускает эту службу, поэтому работа окна Throne не меняется. См. [Установка и службы](@/headless/installation.ru.md#linux-packages).

### APT-репозиторий {#apt-repository}

APT-репозиторий `throneproj.github.io/Throne/apt` содержит пакеты с системным Qt для amd64 и arm64 (см. [Пакеты с системным Qt](#system-qt)). В него попадает каждый стабильный релиз, но не бета-версии, и он подписан. Пакетам с системным Qt нужен Debian 12, Ubuntu 22.04 или новее. В более старых системах установите вместо этого обычный `.deb` со [страницы загрузок](@/downloads.ru.md). Чтобы добавить репозиторий в Debian, Ubuntu и других системах на основе Debian:

```bash
sudo install -d -m 0755 /etc/apt/keyrings
sudo curl -fsSLo /etc/apt/keyrings/throne.asc https://throneproj.github.io/Throne/throne.asc
sudo curl -fsSLo /etc/apt/sources.list.d/throne.list https://throneproj.github.io/Throne/apt/throne.list
sudo apt update
sudo apt install throne
```

После этого `sudo apt upgrade` обновляет и Throne.

Если раньше вы установили обычный `.deb` со [страницы загрузок](@/downloads.ru.md), первая новая версия, которую `apt upgrade` установит из репозитория, заменит его пакетом с системным Qt, потому что оба пакета называются `throne`. Чтобы оставить обычный пакет, не добавляйте репозиторий и устанавливайте новые версии со [страницы загрузок](@/downloads.ru.md).

### RPM-репозиторий {#rpm-repository}

RPM-репозиторий `throneproj.github.io/Throne/rpm` содержит пакеты с системным Qt для x86_64 и aarch64, поэтому ваш дистрибутив должен предоставлять Qt 6.5 или новее. Этим пакетам также нужна glibc 2.34 или новее, поэтому они не устанавливаются в RHEL 8 и его пересборках; там установите обычный `.rpm` со [страницы загрузок](@/downloads.ru.md). В репозиторий попадает каждый стабильный релиз, но не бета-версии, а пакеты и сам репозиторий подписаны.

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

При первом использовании `dnf` и `zypper` просят подтвердить доверие к ключу подписи репозитория.

Если раньше вы установили обычный `.rpm` со [страницы загрузок](@/downloads.ru.md), первая новая версия, которую `dnf upgrade` или `zypper update` установит из репозитория, заменит его пакетом с системным Qt, потому что оба пакета называются `throne`. Чтобы оставить обычный пакет, не добавляйте репозиторий и устанавливайте новые версии со [страницы загрузок](@/downloads.ru.md).

### Пакеты с системным Qt {#system-qt}

Qt — это инструментарий, на котором построено окно Throne. Обычные пакеты содержат собственную копию Qt. Пакеты `-system-qt` вместо этого используют библиотеки Qt 6 вашего дистрибутива, и менеджер пакетов устанавливает их как зависимости. Используйте пакет с системным Qt, если обычный пакет не запускается, например потому что ваш процессор слишком старый (см. [Устранение неполадок](@/get_started/installation.ru.md#troubleshooting)), или если вы хотите, чтобы Throne использовал тему Qt вашего рабочего стола. См. также [FAQ](@/help/faq.ru.md#deb-variants).

## Менеджеры пакетов {#package-managers}

{% alert_warning() %}
Пакеты WinGet, Scoop и Nix, а также пакеты AUR, кроме `throne`, создают участники сообщества, а не разработчики Throne. Эти пакеты могут отставать от последнего релиза и работать не так, как официальные сборки. О проблемах с таким пакетом сообщайте его сопровождающему. Если вы не уверены, связана ли проблема с Throne или с пакетом, сначала проверьте официальную сборку со [страницы загрузок](@/downloads.ru.md). См. также [FAQ](@/help/faq.ru.md#third-party-packages).
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

Cask берётся из собственного Homebrew tap Throne, и процесс выпуска релизов Throne обновляет его для каждого стабильного релиза, но не для бета-версий. Он устанавливает `Throne.app` для Apple Silicon или Intel в `/Applications` и требует macOS 14.4 или новее. Для Mac с процессором Intel и macOS с 10.15 по 14.3 используйте ZIP-архив `macoslegacy` из раздела [macOS](#macos). Throne не нотаризован Apple, поэтому cask снимает флаг карантина после установки приложения, и команда `xattr` вам не нужна.

Основной список cask в Homebrew принимает только приложения, нотаризованные Apple, поэтому `brew install --cask throne` не находит Throne. Всегда указывайте полное имя `throneproj/tap/throne`.

Обновляйте Throne командой `brew upgrade --cask throne`. После обновления Throne снова запрашивает права, нужные для режима TUN. `brew uninstall --zap --cask throne` также удаляет ваши данные в `~/Library/Preferences/Throne`.

Для Debian, Ubuntu, Fedora, RHEL и openSUSE у Throne есть собственные [APT-репозиторий](#apt-repository) и [RPM-репозиторий](#rpm-repository).

### Arch Linux (AUR) {#aur}

Throne есть в Arch User Repository под именем `throne`. Установите его с помощью AUR-помощника, например `yay -S throne` или `paru -S throne`.

Пакет `throne` собирает Throne из исходного кода и устанавливает его в `/usr/lib/throne`. Его скрипт сборки хранится в репозитории Throne, а процесс выпуска релизов Throne обновляет пакет для каждого стабильного релиза. Throne хранит ваши данные в `~/.config/Throne`.

### NixOS {#nixos}

Добавьте это в конфигурацию NixOS:

```nix
programs.throne = {
  enable = true;
  tunMode.enable = true; # optional, needed for TUN mode
};
```

### Nix {#nix}

В NixOS канал обычно называется `nixos`:

```bash
nix-env -iA nixos.throne
```

В других дистрибутивах канал обычно называется `nixpkgs`, поэтому используйте `nixpkgs.throne`. Чтобы попробовать Throne без установки, выполните `nix-shell -p throne`.

## Сборка из исходного кода {#build-from-source}

Throne состоит из двух частей, которые собираются отдельно: интерфейса (`Throne`, написан на C++ с Qt) и ядра (`ThroneCore`, написано на Go). Без ядра интерфейс не может подключаться, поэтому ядро должно лежать рядом с исполняемым файлом `Throne`. В репозитории нет подмодулей Git, поэтому достаточно обычного `git clone`.

| Часть | Требования |
|---|---|
| Интерфейс | CMake 3.20 или новее, компилятор C++20, Qt 6.2 или новее (Widgets, Network, LinguistTools, а в Linux также DBus), заголовочные файлы X11 и xcb для разработки в Linux |
| Ядро | Go 1.26 или новее, `protoc`, `protoc-gen-go` и `protoc-gen-go-grpc`. CGO в Linux и macOS: в Linux — набор инструментов Chromium из cronet-go (он нужен для outbound NaïveProxy), в macOS — инструменты командной строки Xcode. |

Пример для Ubuntu 22.04 или новее на x64:

1. Установите инструменты сборки и Qt:

   ```bash
   sudo apt update
   sudo apt install build-essential cmake ninja-build git curl protobuf-compiler libx11-dev \
     qt6-base-dev qt6-tools-dev qt6-tools-dev-tools qt6-l10n-tools libqt6svg6-dev \
     qt6-translations-l10n libglx-dev libgl1-mesa-dev
   ```

2. Установите Go 1.26 или новее с [go.dev/dl](https://go.dev/dl/). В большинстве дистрибутивов пакеты Go более старые.
3. Соберите интерфейс. Для сборки в папке `build` нужен файл `srslist.h` — список имён наборов правил:

   ```bash
   git clone https://github.com/throneproj/Throne.git ~/Throne
   cd ~/Throne
   mkdir build
   cd build
   curl -fLso srslist.h https://raw.githubusercontent.com/throneproj/routeprofiles/rule-set/srslist.h
   cmake -GNinja -DCMAKE_BUILD_TYPE=Release ..
   ninja
   ```

4. Установите плагины protobuf тех версий, которые используют официальные сборки, и добавьте папку `bin` из Go в `PATH`:

   ```bash
   go install google.golang.org/protobuf/cmd/protoc-gen-go@v1.36.12
   go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@v1.6.2
   export PATH="$PATH:$(go env GOPATH)/bin"
   ```

5. Скачайте набор инструментов Chromium. Замените `<commit>` на коммит cronet-go, который извлекается на шаге `Clone cronet-go` в `.github/workflows/build.yml`:

   ```bash
   git clone https://github.com/throneproj/cronet-go.git ~/cronet-go
   cd ~/cronet-go
   git checkout <commit>
   git submodule update --init --recursive --depth=1
   go run ./cmd/build-naive --target=linux/amd64 download-toolchain
   eval "$(go run ./cmd/build-naive --target=linux/amd64 env --export)"
   ```

   Последняя команда задаёт `CC`, `CXX` и `CGO_LDFLAGS` только для текущего терминала. Выполните следующий шаг в том же терминале.

6. Соберите ядро и скопируйте его в папку с интерфейсом. `build_go.sh` удаляет папку `DEST` перед сборкой, поэтому укажите для неё отдельную папку:

   ```bash
   cd ~/Throne
   GOOS=linux GOARCH=amd64 DEST="$PWD/deployment/linux-amd64" ./script/build_go.sh
   cp deployment/linux-amd64/ThroneCore build/
   ```

7. Запустите Throne командой `./build/Throne`.

Для ARM64 используйте `--target=linux/arm64` и `GOARCH=arm64`.

- **macOS:** добавьте `-DNKR_PACKAGE_MACOS=1` к команде `cmake` — так собирается `Throne.app`. Соберите ядро с `GOOS=darwin` и скопируйте `ThroneCore` в `Throne.app/Contents/MacOS/`.
- **Windows:** в официальных сборках интерфейс компилируется с помощью MSVC и Ninja, а ядро кросс-компилируется в Linux с `GOOS=windows` (для Windows скрипт отключает CGO). Скопируйте `ThroneCore.exe` и `libcronet.dll` из папки `DEST` в папку с `Throne.exe`.

Точные шаги официальных сборок описаны в `.github/workflows/build.yml` в [репозитории Throne](https://github.com/throneproj/Throne). Legacy-сборки для Windows 7 и macOS 10.15 компилируют ядро с помощью [форка Go от MetaCubeX](https://github.com/MetaCubeX/go) 1.26, потому что официальный Go больше не поддерживает эти системы.

## Обновление {#updating}

Копии из ZIP-архива и установка через установщик Windows для текущего пользователя (вариант по умолчанию) умеют обновляться сами: нажмите `Программа` (Program) → `Проверить наличие обновлений` (Check For Update) (до 1.4.0 — `Утилиты` (Tools) → `Проверить наличие обновлений`), затем `Обновить` (Update). Все остальные копии обновляйте тем же способом, которым устанавливали: запустите новый установщик, установите новый пакет `.deb` или `.rpm`, снова запустите установочный скрипт, замените `Throne.app` в macOS или воспользуйтесь менеджером пакетов. Ваши профили и настройки сохраняются.

Начиная с 1.4.0 `Проверить наличие обновлений` не показывает кнопку `Обновить`, пока из той же установки работает [headless Throne](@/headless/_index.ru.md). Сначала остановите headless Throne.

Подробности, бета-версии и резервное копирование описаны на странице [Резервное копирование, обновление и перенос](@/guides/backup.ru.md#updating). После обновления в Linux или macOS Throne может снова запросить права, необходимые для режима TUN.

## Устранение неполадок {#troubleshooting}

- **Предупреждение антивируса.** Некоторые антивирусы помечают Throne или `ThroneCore` как опасные — в основном потому, что программа обновления скачивает и заменяет файлы программы. См. [FAQ](@/help/faq.ru.md#antivirus).
- **Throne не запускается и сообщает «Incompatible processor. This Qt build requires the following features: sse4.2 popcnt» (несовместимый процессор).** Ваш процессор слишком стар для встроенного Qt. В Windows используйте `windowslegacy64.zip`. В Linux используйте пакет `-system-qt`. См. также [FAQ](@/help/faq.ru.md#old-systems).
- **Linux: Throne сообщает, что не может загрузить плагин платформы Qt «xcb».** Если Throne использует Qt вашей системы, установите `libxcb-cursor0` (Debian, Ubuntu) или `xcb-util-cursor` (Fedora, RHEL). ZIP-файл и обычные пакеты уже содержат эту библиотеку; если ошибка появляется с ними, используйте пакет `-system-qt`.
- **Linux с GNOME: нет значка в трее.** Установите для GNOME расширение AppIndicator.
- **macOS: «Throne is damaged and can't be opened» (Throne повреждён, и его не удаётся открыть).** Флаг карантина всё ещё установлен. Выполните команду `xattr` из инструкции для [macOS](@/get_started/installation.ru.md#macos).

О проблемах, возникающих после установки, см. [Устранение неполадок](@/help/troubleshooting.ru.md#startup).

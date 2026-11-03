+++
title = "安装"
description = "在 Windows、macOS 或 Linux 上安装 Throne、保持更新，或从源码构建。"
weight = 1
toc = true
+++

本页介绍如何在 Windows、macOS 和 Linux 上安装 Throne、如何更新，以及如何从源码构建。安装文件请从[下载](@/downloads.zh.md)页面获取。Android 版请参阅[安装与升级](@/android/installation.zh.md)。

## Windows {#windows}

### 下载哪个文件 {#windows-files}

| 你的电脑 | 文件 |
|---|---|
| 任意 Windows 电脑（不确定时选这个） | `Throne-<version>-windows-universal-installer.exe` |
| Windows 10 1809 或更高版本，x64 | `Throne-<version>-windows64.zip` |
| ARM64 版 Windows | `Throne-<version>-windows-arm64.zip` |
| Windows 7 SP1 至 Windows 10 1803（x64），或不支持 SSE4.2 的处理器 | `Throne-<version>-windowslegacy64.zip` |
| 32 位（x86）Windows 7 SP1 或更高版本 | `Throne-<version>-windows32.zip` |

### 安装程序 {#windows-installer}

通用安装程序是最简单的选择：

- 它会为你的用户账户将 Throne 安装到 `%LOCALAPPDATA%\Throne`。你不需要管理员权限。
- 它会为你的电脑选择合适的版本：x64、ARM64 或 32 位。在早于 Windows 10 1809 的 x64 Windows 上，它会安装 legacy 版本。
- 它会将 Throne 添加到开始菜单和桌面。
- 通过安装程序安装的 Throne 每次启动时都会注册 `throne://` 链接，因此这些链接会在 Throne 中打开。参见[深度链接](@/advanced/deeplinks.zh.md#receive)。

Throne 将你的配置档和设置保存在安装文件夹内的 `config` 文件夹中。如果你曾用旧版安装程序（1.3.0 之前）安装 Throne，新安装程序会使用同一个文件夹，并保留你的数据。

安装程序也可以为所有用户安装 Throne，安装位置为 `Program Files`。这需要管理员权限。由于 Throne 无法写入 `Program Files`，它会改为将你的数据保存在 `%LOCALAPPDATA%\Throne\config` 中，并且无法使用一键更新。

要卸载 Throne，请在 Windows 的已安装应用列表中将其删除。卸载程序会询问是否同时删除你的配置档、设置和日志。它还会移除 `throne://` 链接的注册。

### 便携版 ZIP {#windows-zip}

1. 下载适合你系统的 ZIP（见上表）。
2. 将其解压到你可以写入文件的文件夹，例如你的用户文件夹中的某个文件夹。
3. 打开 `Throne` 文件夹，运行 `Throne.exe`。

请将该文件夹中的所有文件放在一起。Throne 会将你的数据保存在 `Throne.exe` 旁边的 `config` 文件夹中，因此你可以移动或备份整个文件夹。如果 Throne 无法写入自己的文件夹，它会改用 `%LOCALAPPDATA%\Throne\config`。

ZIP 版默认不注册 `throne://` 链接。要开启此功能，请勾选 `设置`（Settings）→ `基本设置`（Basic Settings）→ `通用`（Common）→ `启动时注册 throne:// 链接`（Register throne:// links at startup）（仅在高级模式下可用，参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）。

## macOS {#macos}

| 你的 Mac | 文件 |
|---|---|
| Apple Silicon，macOS 14.4 或更高版本 | `Throne-<version>-macos-arm64.zip` |
| Intel，macOS 14.4 或更高版本 | `Throne-<version>-macos-amd64.zip` |
| Intel，macOS 10.15 至 14.3 | `Throne-<version>-macoslegacy-amd64.zip` |

自 1.4.0 起，普通版本需要 macOS 14.4 或更高版本；在 Throne 1.3 及更早版本中，普通版本需要 macOS 13 或更高版本。

你也可以通过 [Homebrew](#homebrew) 安装 Throne：它会替你完成下面的步骤，并可以用 `brew upgrade` 更新 Throne。

1. 下载 ZIP 并解压。其中包含一个 `Throne` 文件夹，里面有 `Throne.app`。
2. 首次打开之前，将 `Throne.app` 移动到 `/Applications`。为了使用 TUN 模式，Throne 会通过“终端”（Terminal）为其核心授予 root 权限，而当应用位于 `~/Downloads` 中时，这一步可能会失败。
3. Throne 没有使用 Apple 证书签名，因此需要在终端中移除隔离标记。如果不执行这一步，macOS 会提示“Throne is damaged and can't be opened”（Throne 已损坏，无法打开）。

   ```bash
   xattr -d com.apple.quarantine /Applications/Throne.app
   ```

4. 打开 Throne。

Throne 将你的数据保存在你的用户文件夹中，而不是 `Throne.app` 内部，因此替换应用不会丢失配置档。`设置` → `打开配置文件夹`（Open Config Folder）会显示该文件夹（仅在高级模式下可用）。TUN 模式需要管理员账户；参见 [TUN 模式](@/guides/tun_mode.zh.md#privileges)。

## Linux {#linux}

| 文件 | 用途 |
|---|---|
| 安装脚本（见下文） | 任意发行版。将 ZIP 版本安装到 `/opt/Throne`。 |
| `Throne-<version>-linux-amd64.zip`、`…-linux-arm64.zip` | 放在你自选文件夹中的便携版 |
| `Throne-<version>-debian-amd64.deb`、`…-debian-arm64.deb` | Debian、Ubuntu、Linux Mint 及其他基于 Debian 的系统 |
| `Throne-<version>-fedora-amd64.rpm`、`…-fedora-arm64.rpm` | Fedora 及基于 RHEL 的系统（自 1.3.0 起） |
| 以 `-system-qt.deb` 或 `-system-qt.rpm` 结尾的文件 | 与上面相同的软件包，但使用系统自带的 Qt 库 |
| [APT 软件源](#apt-repository)和 [RPM 软件源](#rpm-repository) | 使用系统 Qt 的软件包，由你的包管理器保持更新 |

自 1.4.0 起，ZIP 文件、安装脚本以及普通的 `.deb` 和 `.rpm` 软件包需要 glibc 2.28 或更高版本，可在 Debian 10、Ubuntu 20.04、RHEL 8（以及 AlmaLinux 或 Rocky Linux 8）、Raspberry Pi OS（64 位）bullseye 及更新的系统上运行。`-system-qt` 软件包需要 glibc 2.34 或更高版本以及发行版自带的 Qt 6，例如 Debian 12 或 Ubuntu 22.04。在 1.4.0 之前，所有 Linux 版本都需要 glibc 2.34 或更高版本，包含 Qt 的 ARM64 文件需要 glibc 2.38 或更高版本。

{% alert_warning() %}
不要使用 `sudo` 或以 root 身份启动 Throne。为了使用 TUN 模式，Throne 只会为其核心 `ThroneCore` 授予 root 权限，并通过 `pkexec` 请求输入你的密码。参见 [TUN 模式](@/guides/tun_mode.zh.md#privileges)。
{% end %}

### 安装脚本 {#linux-script}

该脚本会下载适合你处理器的版本，将其安装到 `/opt/Throne`，并将 Throne 添加到应用程序菜单。它需要 Python 3 和 root 权限：

```bash
curl -fsSL https://raw.githubusercontent.com/throneproj/Throne/dev/script/install_linux.py | sudo python3
```

脚本会显示最新的稳定版和不稳定版；如果已经安装了 Throne，还会显示已安装的版本。选择 `Install` 或 `Uninstall`。选择 `Install` 时，还需要选择分支：`Stable` 或 `Unstable`。

再次运行同一条命令即可更新或卸载 Throne。卸载会移除 `/opt/Throne` 和菜单项，但会保留 `~/.config/Throne` 中的设置。

### 便携版 ZIP {#linux-zip}

ZIP 中包含一个 `Throne` 文件夹。Throne 会将你的数据保存在其中的 `config` 文件夹里。

```bash
unzip Throne-x.x.x-linux-amd64.zip
cd Throne
./Throne
```

### Debian 和 Ubuntu（.deb） {#deb}

```bash
sudo apt install ./Throne-x.x.x-debian-amd64.deb
```

要通过 `apt upgrade` 获取新版本，请改用 [APT 软件源](#apt-repository)。

### Fedora 和 RHEL（.rpm） {#rpm}

```bash
sudo dnf install ./Throne-x.x.x-fedora-amd64.rpm
```

要通过 `dnf upgrade` 或 `zypper update` 获取新版本，请改用 [RPM 软件源](#rpm-repository)。

这两种软件包都会将 Throne 安装到 `/opt/Throne`，并将其添加到应用程序菜单。之后 Throne 会将你的数据保存在 `~/.config/Throne` 中。

自 1.4.0 起，这些软件包还会为[无界面 Throne](@/headless/_index.zh.md) 安装一个 systemd 服务 `/usr/lib/systemd/system/throne.service` 及其设置文件 `/etc/throne/throne.env`。软件包从不启用或启动该服务，因此它不会改变 Throne 窗口的工作方式。参见[安装与服务](@/headless/installation.zh.md#linux-packages)。

### APT 软件源 {#apt-repository}

位于 `throneproj.github.io/Throne/apt` 的 APT 软件源提供适用于 amd64 和 arm64 的使用系统 Qt 的软件包（参见[使用系统 Qt 的软件包](#system-qt)）。它包含每一个稳定版，但不包含 Beta 版，并且经过签名。使用系统 Qt 的软件包需要 Debian 12、Ubuntu 22.04 或更高版本。在更旧的系统上，请改为从[下载](@/downloads.zh.md)页面安装普通的 `.deb`。要在 Debian、Ubuntu 及其他基于 Debian 的系统上添加该软件源：

```bash
sudo install -d -m 0755 /etc/apt/keyrings
sudo curl -fsSLo /etc/apt/keyrings/throne.asc https://throneproj.github.io/Throne/throne.asc
sudo curl -fsSLo /etc/apt/sources.list.d/throne.list https://throneproj.github.io/Throne/apt/throne.list
sudo apt update
sudo apt install throne
```

之后，`sudo apt upgrade` 也会更新 Throne。

如果你之前从[下载](@/downloads.zh.md)页面安装了普通的 `.deb`，`apt upgrade` 从软件源安装的第一个新版本会把它替换为使用系统 Qt 的软件包，因为这两个软件包都叫 `throne`。如果想继续使用普通软件包，请不要添加软件源，而是从[下载](@/downloads.zh.md)页面安装新版本。

### RPM 软件源 {#rpm-repository}

位于 `throneproj.github.io/Throne/rpm` 的 RPM 软件源提供适用于 x86_64 和 aarch64 的使用系统 Qt 的软件包，因此你的发行版必须提供 Qt 6.5 或更高版本。这些软件包还需要 glibc 2.34 或更高版本，因此无法安装在 RHEL 8 及其重构版上；请在这些系统上从[下载](@/downloads.zh.md)页面安装普通的 `.rpm`。该软件源包含每一个稳定版，但不包含 Beta 版，软件包和软件源都经过签名。

Fedora 和 RHEL 9 或更高版本：

```bash
sudo curl -fsSLo /etc/yum.repos.d/throne.repo https://throneproj.github.io/Throne/rpm/throne.repo
sudo dnf install throne --refresh
```

openSUSE 和 SLES：

```bash
sudo zypper addrepo -fc https://throneproj.github.io/Throne/rpm/throne.repo
sudo zypper install throne
```

首次使用时，`dnf` 和 `zypper` 会请你确认是否信任该软件源的签名密钥。

如果你之前从[下载](@/downloads.zh.md)页面安装了普通的 `.rpm`，`dnf upgrade` 或 `zypper update` 从软件源安装的第一个新版本会把它替换为使用系统 Qt 的软件包，因为这两个软件包都叫 `throne`。如果想继续使用普通软件包，请不要添加软件源，而是从[下载](@/downloads.zh.md)页面安装新版本。

### 使用系统 Qt 的软件包 {#system-qt}

Qt 是 Throne 窗口所使用的工具包。普通软件包自带一份 Qt。`-system-qt` 软件包则改用你的发行版提供的 Qt 6 库，由包管理器将其作为依赖安装。当普通软件包无法启动时（例如因为你的处理器太旧，参见[故障排除](@/get_started/installation.zh.md#troubleshooting)），或者当你希望 Throne 使用桌面环境的 Qt 主题时，请使用 system-Qt 软件包。另请参阅[常见问题](@/help/faq.zh.md#deb-variants)。

## 包管理器 {#package-managers}

{% alert_warning() %}
WinGet、Scoop 和 Nix 软件包，以及除 `throne` 之外的 AUR 软件包，由社区成员制作，而不是由 Throne 开发者制作。它们可能比最新版本旧，行为也可能与官方版本不同。请将这类软件包的问题报告给其维护者。如果你不确定问题来自 Throne 还是软件包，请先测试[下载](@/downloads.zh.md)页面中的官方版本。另请参阅[常见问题](@/help/faq.zh.md#third-party-packages)。
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

该 cask 来自 Throne 自己的 Homebrew tap，Throne 的发布工作流会在每个稳定版发布时更新它，但不包含 Beta 版。它会将适用于 Apple Silicon 或 Intel 的 `Throne.app` 安装到 `/Applications`，需要 macOS 14.4 或更高版本。运行 macOS 10.15 至 14.3 的 Intel Mac 请使用 [macOS](#macos) 中的 `macoslegacy` ZIP。Throne 未经过 Apple 公证，因此 cask 会在安装应用后移除隔离标记，你无需运行 `xattr` 命令。

Homebrew 的主 cask 列表只接受经过 Apple 公证的应用，因此 `brew install --cask throne` 找不到 Throne。请始终使用完整名称 `throneproj/tap/throne`。

使用 `brew upgrade --cask throne` 更新 Throne。更新后，Throne 会再次请求 TUN 模式所需的权限。`brew uninstall --zap --cask throne` 还会删除你保存在 `~/Library/Preferences/Throne` 中的数据。

对于 Debian、Ubuntu、Fedora、RHEL 和 openSUSE，Throne 有自己的 [APT 软件源](#apt-repository)和 [RPM 软件源](#rpm-repository)。

### Arch Linux（AUR） {#aur}

Throne 已收录在 Arch 用户软件仓库（AUR）中，包名为 `throne`。请使用 AUR 助手安装，例如 `yay -S throne` 或 `paru -S throne`。

`throne` 软件包会从源码构建 Throne，并将其安装到 `/usr/lib/throne`。它的构建脚本保存在 Throne 仓库中，Throne 的发布工作流会在每个稳定版发布时更新该软件包。Throne 会将你的数据保存在 `~/.config/Throne` 中。

### NixOS {#nixos}

将以下内容添加到你的 NixOS 配置中：

```nix
programs.throne = {
  enable = true;
  tunMode.enable = true; # optional, needed for TUN mode
};
```

### Nix {#nix}

在 NixOS 上，频道通常名为 `nixos`：

```bash
nix-env -iA nixos.throne
```

在其他发行版上，频道通常名为 `nixpkgs`，因此请使用 `nixpkgs.throne`。如果想在不安装的情况下试用 Throne，请运行 `nix-shell -p throne`。

## 从源码构建 {#build-from-source}

Throne 由两部分组成，需要分别构建：界面（`Throne`，使用 C++ 和 Qt 编写）和核心（`ThroneCore`，使用 Go 编写）。界面没有核心就无法连接，因此核心必须放在 `Throne` 可执行文件旁边。该仓库没有 Git 子模块，所以普通的 `git clone` 就足够了。

| 部分 | 要求 |
|---|---|
| 界面 | CMake 3.20 或更高版本、C++20 编译器、Qt 6.2 或更高版本（Widgets、Network、LinguistTools，在 Linux 上还需要 DBus），在 Linux 上还需要 X11 和 xcb 开发头文件 |
| 核心 | Go 1.26 或更高版本，以及 `protoc`、`protoc-gen-go` 和 `protoc-gen-go-grpc`。在 Linux 和 macOS 上需要 CGO：Linux 上需要 cronet-go 提供的 Chromium 工具链（NaïveProxy 出站需要它），macOS 上需要 Xcode 命令行工具。 |

以 x64 上的 Ubuntu 22.04 或更高版本为例：

1. 安装构建工具和 Qt：

   ```bash
   sudo apt update
   sudo apt install build-essential cmake ninja-build git curl protobuf-compiler libx11-dev \
     qt6-base-dev qt6-tools-dev qt6-tools-dev-tools qt6-l10n-tools libqt6svg6-dev \
     qt6-translations-l10n libglx-dev libgl1-mesa-dev
   ```

2. 从 [go.dev/dl](https://go.dev/dl/) 安装 Go 1.26 或更高版本。大多数发行版自带的 Go 软件包版本较旧。
3. 构建界面。构建需要 `build` 文件夹中有 `srslist.h`，即规则集名称列表：

   ```bash
   git clone https://github.com/throneproj/Throne.git ~/Throne
   cd ~/Throne
   mkdir build
   cd build
   curl -fLso srslist.h https://raw.githubusercontent.com/throneproj/routeprofiles/rule-set/srslist.h
   cmake -GNinja -DCMAKE_BUILD_TYPE=Release ..
   ninja
   ```

4. 安装与官方构建所用版本相同的 protobuf 插件，并将 Go 的 `bin` 文件夹添加到 `PATH`：

   ```bash
   go install google.golang.org/protobuf/cmd/protoc-gen-go@v1.36.12
   go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@v1.6.2
   export PATH="$PATH:$(go env GOPATH)/bin"
   ```

5. 下载 Chromium 工具链。将 `<commit>` 替换为 `.github/workflows/build.yml` 中 `Clone cronet-go` 步骤所检出的 cronet-go 提交：

   ```bash
   git clone https://github.com/throneproj/cronet-go.git ~/cronet-go
   cd ~/cronet-go
   git checkout <commit>
   git submodule update --init --recursive --depth=1
   go run ./cmd/build-naive --target=linux/amd64 download-toolchain
   eval "$(go run ./cmd/build-naive --target=linux/amd64 env --export)"
   ```

   最后一条命令只会为当前终端设置 `CC`、`CXX` 和 `CGO_LDFLAGS`。请在同一个终端中执行下一步。

6. 构建核心，并将其复制到界面旁边。`build_go.sh` 会在构建前删除 `DEST` 文件夹，因此请为它指定一个单独的文件夹：

   ```bash
   cd ~/Throne
   GOOS=linux GOARCH=amd64 DEST="$PWD/deployment/linux-amd64" ./script/build_go.sh
   cp deployment/linux-amd64/ThroneCore build/
   ```

7. 使用 `./build/Throne` 启动 Throne。

在 ARM64 上，请使用 `--target=linux/arm64` 和 `GOARCH=arm64`。

- **macOS：** 在 `cmake` 命令中添加 `-DNKR_PACKAGE_MACOS=1`，这样会构建出 `Throne.app`。使用 `GOOS=darwin` 构建核心，并将 `ThroneCore` 复制到 `Throne.app/Contents/MacOS/` 中。
- **Windows：** 官方构建使用 MSVC 和 Ninja 编译界面，并在 Linux 上以 `GOOS=windows` 交叉编译核心（脚本会为 Windows 关闭 CGO）。将 `DEST` 文件夹中的 `ThroneCore.exe` 和 `libcronet.dll` 复制到 `Throne.exe` 旁边。

官方构建的具体步骤见 [Throne 仓库](https://github.com/throneproj/Throne)中的 `.github/workflows/build.yml`。适用于 Windows 7 和 macOS 10.15 的 legacy 版本使用 [MetaCubeX 的 Go 分支](https://github.com/MetaCubeX/go) 1.26 编译核心，因为官方 Go 已不再支持这些系统。

## 更新 {#updating}

ZIP 版以及 Windows 安装程序默认的按用户安装可以自行更新：点击 `程序`（Program）→ `检查更新`（Check For Update）（1.4.0 之前为 `工具`（Tools）→ `检查更新`），然后点击 `更新`（Update）。其他安装方式请按原来的安装方式更新：运行新的安装程序、安装新的 `.deb` 或 `.rpm` 软件包、再次运行安装脚本、在 macOS 上替换 `Throne.app`，或使用你的包管理器。你的配置档和设置会保留。

自 1.4.0 起，当有[无界面 Throne](@/headless/_index.zh.md) 从同一安装位置运行时，`检查更新`（Check For Update）不会显示 `更新`（Update）按钮。请先停止无界面 Throne。

有关详细信息、Beta 版和备份，请参阅[备份、更新与迁移](@/guides/backup.zh.md#updating)。在 Linux 或 macOS 上更新后，Throne 可能会再次请求 TUN 模式所需的权限。

## 故障排除 {#troubleshooting}

- **杀毒软件报警。** 一些杀毒软件会标记 Throne 或 `ThroneCore`，主要是因为更新程序会下载并替换程序文件。参见[常见问题](@/help/faq.zh.md#antivirus)。
- **Throne 无法启动，并提示“Incompatible processor. This Qt build requires the following features: sse4.2 popcnt”（处理器不兼容，此 Qt 版本需要 sse4.2 和 popcnt 指令）。** 你的处理器对于自带的 Qt 来说太旧了。在 Windows 上，请使用 `windowslegacy64.zip`。在 Linux 上，请使用 `-system-qt` 软件包。另请参阅[常见问题](@/help/faq.zh.md#old-systems)。
- **Linux：Throne 提示无法加载 Qt 平台插件“xcb”。** 如果 Throne 使用的是系统的 Qt，请安装 `libxcb-cursor0`（Debian、Ubuntu）或 `xcb-util-cursor`（Fedora、RHEL）。ZIP 文件和普通软件包已自带该库；如果它们出现此错误，请使用 `-system-qt` 软件包。
- **Linux（GNOME）：没有托盘图标。** 为 GNOME 安装 AppIndicator 扩展。
- **macOS：“Throne is damaged and can't be opened”（Throne 已损坏，无法打开）。** 隔离标记仍然存在。请运行 [macOS](@/get_started/installation.zh.md#macos) 步骤中的 `xattr` 命令。

安装后遇到的问题，请参阅[故障排除](@/help/troubleshooting.zh.md#startup)。

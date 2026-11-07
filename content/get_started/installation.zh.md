+++
title = "安装"
description = "在 Windows、macOS 或 Linux 上安装 Throne、保持更新，或从源码构建。"
weight = 1
toc = true
+++

安装文件请从[下载](@/downloads.zh.md)页面获取。Android 版请参阅[安装与升级](@/android/installation.zh.md)。

## Windows {#windows}

| 你的电脑 | 文件 |
|---|---|
| 任意 Windows 电脑（不确定时选这个） | `Throne-<version>-windows-universal-installer.exe` |
| Windows 10 1809 或更高版本，x64 | `Throne-<version>-windows64.zip` |
| ARM64 版 Windows | `Throne-<version>-windows-arm64.zip` |
| Windows 7 SP1 至 Windows 10 1803（x64），或不支持 SSE4.2 的处理器 | `Throne-<version>-windowslegacy64.zip` |
| 32 位（x86）Windows 7 SP1 或更高版本 | `Throne-<version>-windows32.zip` |

### 安装程序 {#windows-installer}

通用安装程序会为你的电脑选择合适的版本，并将其安装到 `%LOCALAPPDATA%\Throne`，不需要管理员权限。你的数据保存在其中的 `config` 文件夹里。如果改为为所有用户安装到 `Program Files`，Throne 会将你的数据保存在 `%LOCALAPPDATA%\Throne\config` 中，并且无法自行更新。

要卸载 Throne，请在 Windows 的已安装应用列表中将其删除。卸载程序会询问是否同时删除你的配置档和设置。

### 便携版 ZIP {#windows-zip}

将 ZIP 解压到你可以写入文件的文件夹，然后运行 `Throne` 文件夹中的 `Throne.exe`。你的数据保存在它旁边的 `config` 文件夹中，因此你可以移动或备份整个文件夹。

与安装程序不同，ZIP 版只有在你勾选 `设置`（Settings）→ `基本设置`（Basic Settings）→ `通用`（Common）→ `启动时注册 throne:// 链接`（Register throne:// links at startup）之后才会打开 [`throne://` 链接](@/advanced/deeplinks.zh.md#receive)（仅在高级模式下可用，参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）。

## macOS {#macos}

| 你的 Mac | 文件 |
|---|---|
| Apple Silicon，macOS 14.4 或更高版本 | `Throne-<version>-macos-arm64.zip` |
| Intel，macOS 14.4 或更高版本 | `Throne-<version>-macos-amd64.zip` |
| Intel，macOS 10.15 至 14.3 | `Throne-<version>-macoslegacy-amd64.zip` |

按以下步骤安装 ZIP，或者使用 [Homebrew](#homebrew)，它会替你完成这些步骤：

1. 解压 ZIP，并在首次打开之前将 `Throne.app` 移动到 `/Applications`。如果应用位于 `~/Downloads` 中，Throne 可能无法获得 TUN 模式所需的权限。
2. Throne 没有使用 Apple 证书签名，因此需要移除隔离标记，否则 macOS 会提示“Throne is damaged and can't be opened”（Throne 已损坏，无法打开）：

   ```bash
   xattr -d com.apple.quarantine /Applications/Throne.app
   ```

3. 打开 Throne。

你的数据保存在你的用户文件夹中，因此替换 `Throne.app` 不会丢失配置档。TUN 模式需要管理员账户；参见 [TUN 模式](@/guides/tun_mode.zh.md#privileges)。

## Linux {#linux}

| 文件 | 用途 |
|---|---|
| [安装脚本](#linux-script) | 任意发行版。将 ZIP 版本安装到 `/opt/Throne`。 |
| `Throne-<version>-linux-amd64.zip`、`…-linux-arm64.zip` | 放在你自选文件夹中的便携版 |
| `Throne-<version>-debian-amd64.deb`、`…-debian-arm64.deb` | Debian、Ubuntu、Linux Mint 及其他基于 Debian 的系统 |
| `Throne-<version>-fedora-amd64.rpm`、`…-fedora-arm64.rpm` | Fedora 及基于 RHEL 的系统 |
| 以 `-system-qt.deb` 或 `-system-qt.rpm` 结尾的文件 | 与上面相同的软件包，但使用系统的 Qt |
| [APT 软件源](#apt-repository)和 [RPM 软件源](#rpm-repository) | 使用系统 Qt 的软件包，由你的包管理器保持更新 |

ZIP 文件、安装脚本以及普通软件包自带一份 Qt，需要 glibc 2.28 或更高版本：Debian 10、Ubuntu 20.04、RHEL 8（以及 AlmaLinux 或 Rocky Linux 8）、Raspberry Pi OS（64 位）bullseye 或更新的系统。`-system-qt` 软件包使用发行版自带的 Qt 6，需要 glibc 2.34 或更高版本，例如 Debian 12 或 Ubuntu 22.04。当普通软件包无法启动时（例如处理器太旧），或者当你希望 Throne 使用桌面环境的 Qt 主题时，请使用这些软件包。

{% alert_warning() %}
不要使用 `sudo` 或以 root 身份启动 Throne。为了使用 TUN 模式，Throne 只会为其核心授予 root 权限，并通过 `pkexec` 请求输入你的密码。参见 [TUN 模式](@/guides/tun_mode.zh.md#privileges)。
{% end %}

### 安装脚本 {#linux-script}

该脚本需要 Python 3：

```bash
curl -fsSL https://raw.githubusercontent.com/throneproj/Throne/dev/script/install_linux.py | sudo python3
```

选择 `Install` 和分支（`Stable` 或 `Unstable`），或者选择 `Uninstall`。再次运行同一条命令即可更新或卸载。卸载会保留 `~/.config/Throne` 中的设置。

### 便携版 ZIP {#linux-zip}

```bash
unzip Throne-x.x.x-linux-amd64.zip
cd Throne
./Throne
```

你的数据保存在 `Throne` 中的 `config` 文件夹里。

### Debian 和 Ubuntu（.deb） {#deb}

```bash
sudo apt install ./Throne-x.x.x-debian-amd64.deb
```

### Fedora 和 RHEL（.rpm） {#rpm}

```bash
sudo dnf install ./Throne-x.x.x-fedora-amd64.rpm
```

这两种软件包都会将 Throne 安装到 `/opt/Throne`，并将你的数据保存在 `~/.config/Throne` 中。要通过包管理器获取更新，请改用软件源。

### APT 软件源 {#apt-repository}

经过签名的 APT 软件源提供每一个稳定版的使用系统 Qt 的软件包，适用于 amd64 和 arm64。它需要 Debian 12、Ubuntu 22.04 或更高版本；在更旧的系统上，请使用普通的 `.deb`。

```bash
sudo install -d -m 0755 /etc/apt/keyrings
sudo curl -fsSLo /etc/apt/keyrings/throne.asc https://throneproj.github.io/Throne/throne.asc
sudo curl -fsSLo /etc/apt/sources.list.d/throne.list https://throneproj.github.io/Throne/apt/throne.list
sudo apt update
sudo apt install throne
```

之后，`sudo apt upgrade` 也会更新 Throne。

### RPM 软件源 {#rpm-repository}

经过签名的 RPM 软件源提供每一个稳定版的使用系统 Qt 的软件包，适用于 x86_64 和 aarch64。这些软件包需要 Qt 6.5 和 glibc 2.34 或更高版本，因此在 RHEL 8 及其重构版上，请使用普通的 `.rpm`。

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

首次使用时，`dnf` 和 `zypper` 会请你确认是否信任签名密钥。

{% alert_info() %}
如果你之前安装了普通的 `.deb` 或 `.rpm`，从软件源进行的第一次升级会把它替换为使用系统 Qt 的软件包，因为这两个软件包都叫 `throne`。如果想继续使用普通软件包，请不要添加软件源。
{% end %}

## 包管理器 {#package-managers}

{% alert_warning() %}
WinGet、Scoop 和 Nix 软件包，以及除 `throne` 之外的 AUR 软件包，由社区制作。它们可能比最新版本旧，行为也可能与官方版本不同。如果遇到问题，请先测试[下载](@/downloads.zh.md)页面中的官方版本，并将软件包的问题报告给其维护者。
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

官方 tap 会收录每一个稳定版。该 cask 会将 `Throne.app` 安装到 `/Applications` 并移除隔离标记，需要 macOS 14.4 或更高版本；较旧的 Intel Mac 请使用 `macoslegacy` ZIP。请始终使用完整名称：`brew install --cask throne` 找不到 Throne。

使用 `brew upgrade --cask throne` 更新；更新后，Throne 会再次请求 TUN 模式所需的权限。`brew uninstall --zap --cask throne` 还会删除你保存在 `~/Library/Preferences/Throne` 中的数据。

### Arch Linux（AUR） {#aur}

使用 AUR 助手安装 `throne`，例如 `yay -S throne` 或 `paru -S throne`。该软件包会从源码构建 Throne，将其安装到 `/usr/lib/throne`，并随每个稳定版更新。你的数据保存在 `~/.config/Throne` 中。

### Nix {#nix}

在 NixOS 上，将以下内容添加到你的配置中：

```nix
programs.throne = {
  enable = true;
  tunMode.enable = true; # optional, needed for TUN mode
};
```

或者使用 `nix-env` 安装。频道在 NixOS 上通常名为 `nixos`，在其他发行版上通常名为 `nixpkgs`：

```bash
nix-env -iA nixos.throne
```

如果想在不安装的情况下试用 Throne，请运行 `nix-shell -p throne`。

## 从源码构建 {#build-from-source}

Throne 由两部分组成：界面（`Throne`，C++ 和 Qt）和核心（`ThroneCore`，Go）。核心必须放在 `Throne` 可执行文件旁边。

| 部分 | 要求 |
|---|---|
| 界面 | CMake 3.20 或更高版本、C++20 编译器、Qt 6.2 或更高版本（Widgets、Network、LinguistTools，在 Linux 上还需要 DBus），在 Linux 上还需要 X11 和 xcb 开发头文件 |
| 核心 | Go 1.26 或更高版本，以及 `protoc`、`protoc-gen-go` 和 `protoc-gen-go-grpc`。CGO：Linux 上需要 cronet-go 提供的 Chromium 工具链（用于 NaïveProxy），macOS 上需要 Xcode 命令行工具。 |

构建界面。构建需要 `build` 文件夹中有 `srslist.h`，即规则集名称列表：

```bash
git clone https://github.com/throneproj/Throne.git ~/Throne
cd ~/Throne
mkdir build
cd build
curl -fLso srslist.h https://raw.githubusercontent.com/throneproj/routeprofiles/rule-set/srslist.h
cmake -GNinja -DCMAKE_BUILD_TYPE=Release ..
ninja
```

然后用 `script/build_go.sh` 构建核心，并将其复制到界面旁边。在 Linux 上，请先在同一个终端中准备好 cronet-go 工具链。`build_go.sh` 会在构建前删除 `DEST` 文件夹，因此请为它指定一个单独的文件夹：

```bash
cd ~/Throne
GOOS=linux GOARCH=amd64 DEST="$PWD/deployment/linux-amd64" ./script/build_go.sh
cp deployment/linux-amd64/ThroneCore build/
```

在 macOS 上，请在 `cmake` 命令中添加 `-DNKR_PACKAGE_MACOS=1`，使用 `GOOS=darwin` 构建核心，并将 `ThroneCore` 复制到 `Throne.app/Contents/MacOS/` 中。各平台的具体步骤，包括 cronet-go 工具链、protobuf 插件版本、Windows 和 legacy 版本，见 [Throne 仓库](https://github.com/throneproj/Throne)中的 `.github/workflows/build.yml`。

## 更新 {#updating}

Windows 和 Linux 的 ZIP 版以及 Windows 安装程序的按用户安装可以自行更新：`程序`（Program）→ `检查更新`（Check For Update），然后点击 `更新`（Update）。如果有[无界面 Throne](@/headless/_index.zh.md) 从同一安装位置运行，请先停止它。其他安装方式请按原来的安装方式更新：运行新的安装程序、安装新的软件包、再次运行安装脚本、在 macOS 上替换 `Throne.app`，或使用你的包管理器。你的配置档和设置会保留。

在 Linux 或 macOS 上更新后，Throne 可能会再次请求 TUN 模式所需的权限。有关 Beta 版和备份，请参阅[备份、更新与迁移](@/guides/backup.zh.md#updating)。

## 故障排除 {#troubleshooting}

如果安装后 Throne 无法启动或显示错误，请参阅[启动和桌面问题](@/help/troubleshooting.zh.md#startup)。如果杀毒软件标记了 Throne，请参阅[常见问题](@/help/faq.zh.md#antivirus)。

+++
title = "Installation"
description = "Install Throne on Windows, macOS or Linux, keep it up to date, or build it from source."
weight = 1
toc = true
+++

Get the files from [Downloads](@/downloads.md). For Android, see [Installation & Upgrade](@/android/installation.md).

## Windows {#windows}

| Your computer | File |
|---|---|
| Any Windows PC, if you are not sure | `Throne-<version>-windows-universal-installer.exe` |
| Windows 10 1809 or newer, x64 | `Throne-<version>-windows64.zip` |
| Windows on ARM64 | `Throne-<version>-windows-arm64.zip` |
| Windows 7 SP1 to Windows 10 1803 (x64), or a processor without SSE4.2 | `Throne-<version>-windowslegacy64.zip` |
| 32-bit (x86) Windows 7 SP1 or newer | `Throne-<version>-windows32.zip` |

### Installer {#windows-installer}

The universal installer picks the right build for your PC and installs it into `%LOCALAPPDATA%\Throne`, without administrator rights. Your data is kept in its `config` folder. If you install for all users into `Program Files` instead, Throne keeps your data in `%LOCALAPPDATA%\Throne\config` and cannot update itself.

To uninstall, remove Throne in the installed apps of Windows. It asks whether to delete your profiles and settings too.

### Portable ZIP {#windows-zip}

Extract the ZIP to a folder you can write to and run `Throne.exe` from the `Throne` folder. Your data is kept in the `config` folder next to it, so you can move or back up the whole folder.

Unlike the installer, a ZIP copy does not open [`throne://` links](@/advanced/deeplinks.md#receive) until you tick `Settings` → `Basic Settings` → `Common` → `Register throne:// links at startup` (Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)).

## macOS {#macos}

| Your Mac | File |
|---|---|
| Apple Silicon, macOS 14.4 or newer | `Throne-<version>-macos-arm64.zip` |
| Intel, macOS 14.4 or newer | `Throne-<version>-macos-amd64.zip` |
| Intel, macOS 10.15 to 14.3 | `Throne-<version>-macoslegacy-amd64.zip` |

Install the ZIP like this, or use [Homebrew](#homebrew), which does it for you:

1. Extract the ZIP and move `Throne.app` to `/Applications` before you open it the first time. From `~/Downloads`, Throne can fail to get the rights it needs for TUN mode.
2. Throne is not signed by Apple, so remove the quarantine flag, or macOS says it "is damaged and can't be opened":

   ```bash
   xattr -d com.apple.quarantine /Applications/Throne.app
   ```

3. Open Throne.

Your data is kept in your user folder, so you can replace `Throne.app` without losing your profiles. TUN mode needs an administrator account; see [TUN Mode](@/guides/tun_mode.md#privileges).

## Linux {#linux}

| File | Use it for |
|---|---|
| [Install script](#linux-script) | Any distribution. Installs the ZIP build to `/opt/Throne`. |
| `Throne-<version>-linux-amd64.zip`, `…-linux-arm64.zip` | A portable copy in a folder of your choice |
| `Throne-<version>-debian-amd64.deb`, `…-debian-arm64.deb` | Debian, Ubuntu, Linux Mint and other Debian-based systems |
| `Throne-<version>-fedora-amd64.rpm`, `…-fedora-arm64.rpm` | Fedora and RHEL-based systems |
| Files that end in `-system-qt.deb` or `-system-qt.rpm` | The same packages, using the Qt of your system |
| [APT repository](#apt-repository) and [RPM repository](#rpm-repository) | The system-Qt packages, kept up to date by your package manager |

The ZIP, the install script and the normal packages include their own copy of Qt and need glibc 2.28 or newer: Debian 10, Ubuntu 20.04, RHEL 8 (and AlmaLinux or Rocky Linux 8), Raspberry Pi OS (64-bit) bullseye or newer. The `-system-qt` packages use the Qt 6 of your distribution and need glibc 2.34 or newer, for example Debian 12 or Ubuntu 22.04. Use them when the normal package does not start, for example on an old processor, or when you want the Qt theme of your desktop.

{% alert_warning() %}
Do not start Throne with `sudo` or as root. For TUN mode, Throne gives root rights only to its core and asks for your password through `pkexec`. See [TUN Mode](@/guides/tun_mode.md#privileges).
{% end %}

### Install script {#linux-script}

The script needs Python 3:

```bash
curl -fsSL https://raw.githubusercontent.com/throneproj/Throne/dev/script/install_linux.py | sudo python3
```

Choose `Install` and the branch (`Stable` or `Unstable`), or `Uninstall`. Run the same command again to update or uninstall. Uninstalling keeps your settings in `~/.config/Throne`.

### Portable ZIP {#linux-zip}

```bash
unzip Throne-x.x.x-linux-amd64.zip
cd Throne
./Throne
```

Your data is kept in the `config` folder inside `Throne`.

### Debian and Ubuntu (.deb) {#deb}

```bash
sudo apt install ./Throne-x.x.x-debian-amd64.deb
```

### Fedora and RHEL (.rpm) {#rpm}

```bash
sudo dnf install ./Throne-x.x.x-fedora-amd64.rpm
```

Both packages install Throne to `/opt/Throne` and keep your data in `~/.config/Throne`. To get updates through your package manager, use a repository instead.

### APT repository {#apt-repository}

The signed APT repository has the system-Qt packages of every stable release for amd64 and arm64. It needs Debian 12, Ubuntu 22.04 or newer; on older systems, use the normal `.deb`.

```bash
sudo install -d -m 0755 /etc/apt/keyrings
sudo curl -fsSLo /etc/apt/keyrings/throne.asc https://throneproj.github.io/Throne/throne.asc
sudo curl -fsSLo /etc/apt/sources.list.d/throne.list https://throneproj.github.io/Throne/apt/throne.list
sudo apt update
sudo apt install throne
```

After that, `sudo apt upgrade` also updates Throne.

### RPM repository {#rpm-repository}

The signed RPM repository has the system-Qt packages of every stable release for x86_64 and aarch64. They need Qt 6.5 and glibc 2.34 or newer, so on RHEL 8 and its rebuilds, use the normal `.rpm`.

Fedora and RHEL 9 or newer:

```bash
sudo curl -fsSLo /etc/yum.repos.d/throne.repo https://throneproj.github.io/Throne/rpm/throne.repo
sudo dnf install throne --refresh
```

openSUSE and SLES:

```bash
sudo zypper addrepo -fc https://throneproj.github.io/Throne/rpm/throne.repo
sudo zypper install throne
```

The first time, `dnf` and `zypper` ask you to trust the signing key.

{% alert_info() %}
If you installed a normal `.deb` or `.rpm` before, the first upgrade from a repository replaces it with the system-Qt package, because both are named `throne`. To keep the normal package, do not add the repository.
{% end %}

## Package managers {#package-managers}

{% alert_warning() %}
The WinGet, Scoop and Nix packages, and the AUR packages other than `throne`, are made by the community. They can be older than the latest release and work differently from the official builds. If something fails, test an official build from [Downloads](@/downloads.md) first, and report problems with the package to its maintainer.
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

The official tap gets every stable release. The cask installs `Throne.app` to `/Applications`, removes the quarantine flag and needs macOS 14.4 or newer; on older Intel Macs, use the `macoslegacy` ZIP. Always use the full name: `brew install --cask throne` does not find Throne.

Update with `brew upgrade --cask throne`; Throne then asks again for the rights it needs for TUN mode. `brew uninstall --zap --cask throne` also deletes your data in `~/Library/Preferences/Throne`.

### Arch Linux (AUR) {#aur}

Install `throne` with an AUR helper, for example `yay -S throne` or `paru -S throne`. The package builds Throne from source, installs it to `/usr/lib/throne` and gets every stable release. Your data is kept in `~/.config/Throne`.

### Nix {#nix}

On NixOS, add this to your configuration:

```nix
programs.throne = {
  enable = true;
  tunMode.enable = true; # optional, needed for TUN mode
};
```

Or install it with `nix-env`. The channel is usually `nixos` on NixOS and `nixpkgs` on other distributions:

```bash
nix-env -iA nixos.throne
```

To try Throne without installing it, run `nix-shell -p throne`.

## Build from source {#build-from-source}

Throne has two parts: the interface (`Throne`, C++ and Qt) and the core (`ThroneCore`, Go). The core must be next to the `Throne` executable.

| Part | Requirements |
|---|---|
| Interface | CMake 3.20 or newer, a C++20 compiler, Qt 6.2 or newer (Widgets, Network, LinguistTools, and DBus on Linux), X11 and xcb development headers on Linux |
| Core | Go 1.26 or newer, `protoc`, `protoc-gen-go` and `protoc-gen-go-grpc`. CGO: on Linux the Chromium toolchain from cronet-go (for NaïveProxy), on macOS the Xcode command line tools. |

Build the interface. It needs `srslist.h`, the list of rule-set names, in the `build` folder:

```bash
git clone https://github.com/throneproj/Throne.git ~/Throne
cd ~/Throne
mkdir build
cd build
curl -fLso srslist.h https://raw.githubusercontent.com/throneproj/routeprofiles/rule-set/srslist.h
cmake -GNinja -DCMAKE_BUILD_TYPE=Release ..
ninja
```

Then build the core with `script/build_go.sh` and copy it next to the interface. On Linux, set up the cronet-go toolchain in the same terminal first. `build_go.sh` deletes the `DEST` folder before it builds, so give it a folder of its own:

```bash
cd ~/Throne
GOOS=linux GOARCH=amd64 DEST="$PWD/deployment/linux-amd64" ./script/build_go.sh
cp deployment/linux-amd64/ThroneCore build/
```

On macOS, add `-DNKR_PACKAGE_MACOS=1` to `cmake`, build the core with `GOOS=darwin` and copy `ThroneCore` into `Throne.app/Contents/MacOS/`. The exact steps for every platform, including the cronet-go toolchain, the protobuf plugin versions, Windows and the legacy builds, are in `.github/workflows/build.yml` in the [Throne repository](https://github.com/throneproj/Throne).

## Updating {#updating}

Windows and Linux ZIP copies, and per-user installations from the Windows installer, update themselves: `Program` → `Check For Update`, then `Update`. If a [headless Throne](@/headless/_index.md) runs from the same installation, stop it first. Update other copies the way you installed them: run the new installer, install the new package, run the install script again, replace `Throne.app` on macOS, or use your package manager. Your profiles and settings stay.

After an update on Linux or macOS, Throne may ask again for the rights it needs for TUN mode. For beta versions and backups, see [Backup, Updates & Migration](@/guides/backup.md#updating).

## Troubleshooting {#troubleshooting}

If Throne does not start or shows an error after installation, see [Startup and desktop problems](@/help/troubleshooting.md#startup). If your antivirus flags Throne, see the [FAQ](@/help/faq.md#antivirus).

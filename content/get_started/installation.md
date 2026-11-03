+++
title = "Installation"
description = "Install Throne on Windows, macOS or Linux, keep it up to date, or build it from source."
weight = 1
toc = true
+++

This page explains how to install Throne on Windows, macOS and Linux, how to update it, and how to build it from source. Get the files from [Downloads](@/downloads.md). For Android, see [Installation & Upgrade](@/android/installation.md).

## Windows {#windows}

### Which file to download {#windows-files}

| Your computer | File |
|---|---|
| Any Windows PC, if you are not sure | `Throne-<version>-windows-universal-installer.exe` |
| Windows 10 1809 or newer, x64 | `Throne-<version>-windows64.zip` |
| Windows on ARM64 | `Throne-<version>-windows-arm64.zip` |
| Windows 7 SP1 to Windows 10 1803 (x64), or a processor without SSE4.2 | `Throne-<version>-windowslegacy64.zip` |
| 32-bit (x86) Windows 7 SP1 or newer | `Throne-<version>-windows32.zip` |

### Installer {#windows-installer}

The universal installer is the easiest choice:

- It installs Throne for your user account into `%LOCALAPPDATA%\Throne`. You do not need administrator rights.
- It picks the right build for your PC: x64, ARM64 or 32-bit. On x64 Windows older than Windows 10 1809, it installs the legacy build.
- It adds Throne to the Start menu and to the desktop.
- An installed Throne registers `throne://` links at every start, so these links open in Throne. See [Deep Links](@/advanced/deeplinks.md#receive).

Throne keeps your profiles and settings in the `config` folder inside the installation folder. If you installed Throne with the old installer (before 1.3.0), the new installer uses the same folder and keeps your data.

Setup can also install Throne for all users, into `Program Files`. This needs administrator rights. Throne cannot write to `Program Files`, so it keeps your data in `%LOCALAPPDATA%\Throne\config` instead, and the one-click update is not available.

To uninstall Throne, remove it from the list of installed apps in Windows. The uninstaller asks whether to also delete your profiles, settings and logs. It also removes the `throne://` link registration.

### Portable ZIP {#windows-zip}

1. Download the ZIP for your system (see the table above).
2. Extract it to a folder where you can write files, for example a folder in your user profile.
3. Open the `Throne` folder and run `Throne.exe`.

Keep all files of the folder together. Throne saves your data in the `config` folder next to `Throne.exe`, so you can move or back up the whole folder. If Throne cannot write to its folder, it uses `%LOCALAPPDATA%\Throne\config` instead.

ZIP copies do not register `throne://` links by default. To turn this on, tick `Settings` → `Basic Settings` → `Common` → `Register throne:// links at startup` (Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)).

## macOS {#macos}

| Your Mac | File |
|---|---|
| Apple Silicon, macOS 14.4 or newer | `Throne-<version>-macos-arm64.zip` |
| Intel, macOS 14.4 or newer | `Throne-<version>-macos-amd64.zip` |
| Intel, macOS 10.15 to 14.3 | `Throne-<version>-macoslegacy-amd64.zip` |

Since 1.4.0, the normal builds need macOS 14.4 or newer; Throne 1.3 and older needed macOS 13 or newer.

You can also install Throne with [Homebrew](#homebrew), which does the steps below for you and updates Throne with `brew upgrade`.

1. Download the ZIP and extract it. It contains a `Throne` folder with `Throne.app`.
2. Move `Throne.app` to `/Applications` before you open it for the first time. For TUN mode, Throne gives its core root rights through Terminal, and this can fail while the app is in `~/Downloads`.
3. Throne is not signed with an Apple certificate, so remove the quarantine flag in Terminal. Without this step, macOS says that Throne "is damaged and can't be opened".

   ```bash
   xattr -d com.apple.quarantine /Applications/Throne.app
   ```

4. Open Throne.

Throne keeps your data in your user folder, not inside `Throne.app`, so you can replace the app without losing your profiles. `Settings` → `Open Config Folder` shows the folder (Advanced mode only). TUN mode needs an administrator account; see [TUN Mode](@/guides/tun_mode.md#privileges).

## Linux {#linux}

| File | Use it for |
|---|---|
| Install script (below) | Any distribution. Installs the ZIP build to `/opt/Throne`. |
| `Throne-<version>-linux-amd64.zip`, `…-linux-arm64.zip` | A portable copy in a folder of your choice |
| `Throne-<version>-debian-amd64.deb`, `…-debian-arm64.deb` | Debian, Ubuntu, Linux Mint and other Debian-based systems |
| `Throne-<version>-fedora-amd64.rpm`, `…-fedora-arm64.rpm` | Fedora and RHEL-based systems (since 1.3.0) |
| Files that end in `-system-qt.deb` or `-system-qt.rpm` | The same packages, but they use the Qt libraries of your system |
| [APT repository](#apt-repository) and [RPM repository](#rpm-repository) | The system-Qt packages, kept up to date by your package manager |

Since 1.4.0, the ZIP files, the install script and the normal `.deb` and `.rpm` packages need glibc 2.28 or newer. They run on Debian 10, Ubuntu 20.04, RHEL 8 (and AlmaLinux or Rocky Linux 8), Raspberry Pi OS (64-bit) bullseye and newer systems. The `-system-qt` packages need glibc 2.34 or newer and the Qt 6 of your distribution, for example on Debian 12 or Ubuntu 22.04. Before 1.4.0, all Linux builds needed glibc 2.34 or newer, and the ARM64 files that include Qt needed glibc 2.38 or newer.

{% alert_warning() %}
Do not start Throne with `sudo` or as root. For TUN mode, Throne gives root rights only to its core, `ThroneCore`, and asks for your password through `pkexec`. See [TUN Mode](@/guides/tun_mode.md#privileges).
{% end %}

### Install script {#linux-script}

The script downloads the build for your processor, installs it to `/opt/Throne`, and adds Throne to the application menu. It needs Python 3 and root rights:

```bash
curl -fsSL https://raw.githubusercontent.com/throneproj/Throne/dev/script/install_linux.py | sudo python3
```

The script shows the latest stable and unstable versions, and the installed version if there is one. Choose `Install` or `Uninstall`. For `Install`, also choose the branch: `Stable` or `Unstable`.

Run the same command again to update Throne or to uninstall it. Uninstalling removes `/opt/Throne` and the menu entry, but keeps your settings in `~/.config/Throne`.

### Portable ZIP {#linux-zip}

The ZIP contains a `Throne` folder. Throne keeps your data in the `config` folder inside it.

```bash
unzip Throne-x.x.x-linux-amd64.zip
cd Throne
./Throne
```

### Debian and Ubuntu (.deb) {#deb}

```bash
sudo apt install ./Throne-x.x.x-debian-amd64.deb
```

To get new versions with `apt upgrade`, use the [APT repository](#apt-repository) instead.

### Fedora and RHEL (.rpm) {#rpm}

```bash
sudo dnf install ./Throne-x.x.x-fedora-amd64.rpm
```

To get new versions with `dnf upgrade` or `zypper update`, use the [RPM repository](#rpm-repository) instead.

Both packages install Throne to `/opt/Throne` and add it to the application menu. Throne then keeps your data in `~/.config/Throne`.

Since 1.4.0, the packages also install a systemd service for [headless Throne](@/headless/_index.md), `/usr/lib/systemd/system/throne.service`, and its settings file `/etc/throne/throne.env`. The package never enables or starts the service, so it does not change how the Throne window works. See [Installation & Services](@/headless/installation.md#linux-packages).

### APT repository {#apt-repository}

The APT repository at `throneproj.github.io/Throne/apt` has the system-Qt packages for amd64 and arm64 (see [System Qt packages](#system-qt)). It gets every stable release, but no beta versions, and it is signed. The system-Qt packages need Debian 12, Ubuntu 22.04 or newer. On older systems, install the normal `.deb` from [Downloads](@/downloads.md) instead. To add the repository on Debian, Ubuntu and other Debian-based systems:

```bash
sudo install -d -m 0755 /etc/apt/keyrings
sudo curl -fsSLo /etc/apt/keyrings/throne.asc https://throneproj.github.io/Throne/throne.asc
sudo curl -fsSLo /etc/apt/sources.list.d/throne.list https://throneproj.github.io/Throne/apt/throne.list
sudo apt update
sudo apt install throne
```

After that, `sudo apt upgrade` also updates Throne.

If you installed a normal `.deb` from [Downloads](@/downloads.md) before, the first new version that `apt upgrade` installs from the repository replaces it with the system-Qt package, because both packages are named `throne`. To keep the normal package, do not add the repository, and install new versions from [Downloads](@/downloads.md).

### RPM repository {#rpm-repository}

The RPM repository at `throneproj.github.io/Throne/rpm` has the system-Qt packages for x86_64 and aarch64, so your distribution must provide Qt 6.5 or newer. These packages also need glibc 2.34 or newer, so they do not install on RHEL 8 and its rebuilds; install the normal `.rpm` from [Downloads](@/downloads.md) there. The repository gets every stable release, but no beta versions, and the packages and the repository are signed.

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

The first time, `dnf` and `zypper` ask you to trust the signing key of the repository.

If you installed a normal `.rpm` from [Downloads](@/downloads.md) before, the first new version that `dnf upgrade` or `zypper update` installs from the repository replaces it with the system-Qt package, because both packages are named `throne`. To keep the normal package, do not add the repository, and install new versions from [Downloads](@/downloads.md).

### System Qt packages {#system-qt}

Qt is the toolkit of the Throne window. The normal packages include their own copy of Qt. The `-system-qt` packages use the Qt 6 libraries of your distribution instead, and your package manager installs them as dependencies. Use a system-Qt package when the normal package does not start, for example because your processor is too old (see [Troubleshooting](@/get_started/installation.md#troubleshooting)), or when you want Throne to use the Qt theme of your desktop. See also the [FAQ](@/help/faq.md#deb-variants).

## Package managers {#package-managers}

{% alert_warning() %}
The WinGet, Scoop and Nix packages, and the AUR packages other than `throne`, are made by community members, not by the Throne developers. They can be older than the latest release and can work differently from the official builds. Report problems with such a package to its maintainer. If you are not sure whether a problem comes from Throne or from the package, test an official build from [Downloads](@/downloads.md) first. See also the [FAQ](@/help/faq.md#third-party-packages).
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

The cask comes from the Homebrew tap of Throne, and the release workflow of Throne updates it for every stable release, but not for beta versions. It installs `Throne.app` for Apple Silicon or Intel to `/Applications` and needs macOS 14.4 or newer. For Intel Macs on macOS 10.15 to 14.3, use the `macoslegacy` ZIP from [macOS](#macos). Throne is not notarized by Apple, so the cask removes the quarantine flag after it installs the app, and you do not need the `xattr` command.

The main cask list of Homebrew accepts only apps that Apple has notarized, so `brew install --cask throne` does not find Throne. Always use the full name `throneproj/tap/throne`.

Update Throne with `brew upgrade --cask throne`. After an upgrade, Throne asks again for the rights it needs for TUN mode. `brew uninstall --zap --cask throne` also deletes your data in `~/Library/Preferences/Throne`.

For Debian, Ubuntu, Fedora, RHEL and openSUSE, Throne has its own [APT repository](#apt-repository) and [RPM repository](#rpm-repository).

### Arch Linux (AUR) {#aur}

Throne is in the Arch User Repository as `throne`. Install it with an AUR helper, for example `yay -S throne` or `paru -S throne`.

The `throne` package builds Throne from source and installs it to `/usr/lib/throne`. Its build script is kept in the Throne repository, and the release workflow of Throne updates the package for every stable release. Throne keeps your data in `~/.config/Throne`.

### NixOS {#nixos}

Add this to your NixOS configuration:

```nix
programs.throne = {
  enable = true;
  tunMode.enable = true; # optional, needed for TUN mode
};
```

### Nix {#nix}

On NixOS, the channel is usually named `nixos`:

```bash
nix-env -iA nixos.throne
```

On other distributions, the channel is usually named `nixpkgs`, so use `nixpkgs.throne`. To try Throne without installing it, run `nix-shell -p throne`.

## Build from source {#build-from-source}

Throne has two parts that you build separately: the interface (`Throne`, written in C++ with Qt) and the core (`ThroneCore`, written in Go). The interface cannot connect without the core, so the core must be next to the `Throne` executable. The repository has no Git submodules, so a normal `git clone` is enough.

| Part | Requirements |
|---|---|
| Interface | CMake 3.20 or newer, a C++20 compiler, Qt 6.2 or newer (Widgets, Network, LinguistTools, and DBus on Linux), X11 and xcb development headers on Linux |
| Core | Go 1.26 or newer, `protoc`, `protoc-gen-go` and `protoc-gen-go-grpc`. CGO on Linux and macOS: on Linux the Chromium toolchain from cronet-go (the NaïveProxy outbound needs it), on macOS the Xcode command line tools. |

Example for Ubuntu 22.04 or newer on x64:

1. Install the build tools and Qt:

   ```bash
   sudo apt update
   sudo apt install build-essential cmake ninja-build git curl protobuf-compiler libx11-dev \
     qt6-base-dev qt6-tools-dev qt6-tools-dev-tools qt6-l10n-tools libqt6svg6-dev \
     qt6-translations-l10n libglx-dev libgl1-mesa-dev
   ```

2. Install Go 1.26 or newer from [go.dev/dl](https://go.dev/dl/). The Go packages of most distributions are older.
3. Build the interface. The build needs `srslist.h`, the list of rule-set names, in the `build` folder:

   ```bash
   git clone https://github.com/throneproj/Throne.git ~/Throne
   cd ~/Throne
   mkdir build
   cd build
   curl -fLso srslist.h https://raw.githubusercontent.com/throneproj/routeprofiles/rule-set/srslist.h
   cmake -GNinja -DCMAKE_BUILD_TYPE=Release ..
   ninja
   ```

4. Install the protobuf plugins in the versions that the official builds use, and add the `bin` folder of Go to `PATH`:

   ```bash
   go install google.golang.org/protobuf/cmd/protoc-gen-go@v1.36.12
   go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@v1.6.2
   export PATH="$PATH:$(go env GOPATH)/bin"
   ```

5. Download the Chromium toolchain. Replace `<commit>` with the cronet-go commit that the `Clone cronet-go` step of `.github/workflows/build.yml` checks out:

   ```bash
   git clone https://github.com/throneproj/cronet-go.git ~/cronet-go
   cd ~/cronet-go
   git checkout <commit>
   git submodule update --init --recursive --depth=1
   go run ./cmd/build-naive --target=linux/amd64 download-toolchain
   eval "$(go run ./cmd/build-naive --target=linux/amd64 env --export)"
   ```

   The last command sets `CC`, `CXX` and `CGO_LDFLAGS` only for the current terminal. Run the next step in the same terminal.

6. Build the core and copy it next to the interface. `build_go.sh` deletes the `DEST` folder before it builds, so give it a folder of its own:

   ```bash
   cd ~/Throne
   GOOS=linux GOARCH=amd64 DEST="$PWD/deployment/linux-amd64" ./script/build_go.sh
   cp deployment/linux-amd64/ThroneCore build/
   ```

7. Start Throne with `./build/Throne`.

On ARM64, use `--target=linux/arm64` and `GOARCH=arm64`.

- **macOS:** add `-DNKR_PACKAGE_MACOS=1` to the `cmake` command; this builds `Throne.app`. Build the core with `GOOS=darwin` and copy `ThroneCore` into `Throne.app/Contents/MacOS/`.
- **Windows:** the official builds compile the interface with MSVC and Ninja, and cross-compile the core on Linux with `GOOS=windows` (the script turns CGO off for Windows). Copy `ThroneCore.exe` and `libcronet.dll` from the `DEST` folder next to `Throne.exe`.

The exact steps of the official builds are in `.github/workflows/build.yml` in the [Throne repository](https://github.com/throneproj/Throne). The legacy builds for Windows 7 and macOS 10.15 compile the core with the [MetaCubeX fork of Go](https://github.com/MetaCubeX/go) 1.26, because the official Go no longer supports these systems.

## Updating {#updating}

ZIP copies and the default per-user installation of the Windows installer can update themselves: click `Program` → `Check For Update` (`Tools` → `Check For Update` before 1.4.0), then `Update`. Update all other copies the way you installed them: run the new installer, install the new `.deb` or `.rpm` package, run the install script again, replace `Throne.app` on macOS, or use your package manager. Your profiles and settings stay.

Since 1.4.0, `Check For Update` shows no `Update` button while a [headless Throne](@/headless/_index.md) runs from the same installation. Stop the headless Throne first.

For the details, beta versions and backups, see [Backup, Updates & Migration](@/guides/backup.md#updating). After an update on Linux or macOS, Throne may ask again for the rights it needs for TUN mode.

## Troubleshooting {#troubleshooting}

- **Antivirus warning.** Some antivirus programs flag Throne or `ThroneCore`, mostly because the updater downloads and replaces program files. See the [FAQ](@/help/faq.md#antivirus).
- **Throne does not start and reports "Incompatible processor. This Qt build requires the following features: sse4.2 popcnt".** Your processor is too old for the included Qt. On Windows, use `windowslegacy64.zip`. On Linux, use a `-system-qt` package. See also the [FAQ](@/help/faq.md#old-systems).
- **Linux: Throne reports that it could not load the Qt platform plugin "xcb".** With a Throne that uses the Qt of your system, install `libxcb-cursor0` (Debian, Ubuntu) or `xcb-util-cursor` (Fedora, RHEL). The ZIP and the normal packages include this library; if they show the error, use a `-system-qt` package.
- **Linux with GNOME: no tray icon.** Install the AppIndicator extension for GNOME.
- **macOS: "Throne is damaged and can't be opened".** The quarantine flag is still set. Run the `xattr` command from the [macOS](@/get_started/installation.md#macos) steps.

For problems after installation, see [Troubleshooting](@/help/troubleshooting.md#startup).

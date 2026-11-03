+++
title = "Installation & Services"
description = "Install headless Throne as a systemd service, a systemd user unit, a Docker container, a macOS LaunchDaemon or a Windows service."
weight = 10
toc = true
+++

Headless Throne is the normal Throne program started with `--headless`. Install Throne as described in [Installation](@/get_started/installation.md), then let a service manager run it. This page shows how for each system:

| System | Service manager | Runs as | Data directory |
| --- | --- | --- | --- |
| Debian, Ubuntu, Raspberry Pi OS, Fedora, openSUSE | [systemd system unit](#linux-packages) | root (or the user `throne`) | `/var/lib/throne` |
| Any Linux, without root | [systemd user unit](#user-unit) | you | `~/.config/Throne` |
| Docker | [Docker or Docker Compose](#docker) | root in the container | the volume `/var/lib/throne` |
| macOS | [launchd LaunchDaemon](#macos) | root | `/Library/Application Support/Throne` |
| Windows | [Windows service](#windows) | LocalSystem | `C:\ProgramData\Throne` |

After the installation, connect `thronectl` as described in [Managing with thronectl](@/headless/thronectl.md).

## Debian, Ubuntu, Raspberry Pi OS, Fedora, openSUSE {#linux-packages}

The `.deb` and `.rpm` packages from the [releases page](https://github.com/throneproj/Throne/releases) and the package repositories install Throne to `/opt/Throne`, the systemd unit `throne.service` and the environment file `/etc/throne/throne.env`. Pick the package by the system's C library (glibc):

| Package | Needs | Runs on, for example |
| --- | --- | --- |
| `Throne-<version>-debian-amd64.deb`, `…-fedora-amd64.rpm` | glibc 2.28 | Debian 10, Ubuntu 20.04, RHEL 8 and newer, Fedora, openSUSE |
| `…-amd64-system-qt.deb`, `…-amd64-system-qt.rpm` | glibc 2.34 and the system's Qt 6 | Debian 12, Ubuntu 22.04 and newer, Fedora, openSUSE Tumbleweed, with Qt from the distribution |
| `Throne-<version>-debian-arm64.deb`, `…-fedora-arm64.rpm` | glibc 2.28 | Raspberry Pi OS bullseye, Debian 10, Ubuntu 20.04 and newer on arm64 |
| `Throne-<version>-debian-arm64-system-qt.deb` | glibc 2.34 and the system's Qt 6 | Raspberry Pi OS bookworm, Debian 12, Ubuntu 22.04 and newer on arm64 |

Each package depends on the glibc version its files need, so the package manager refuses a package that is too new for the system instead of installing a Throne that cannot start. On a Raspberry Pi, use the 64-bit Raspberry Pi OS. On bookworm and newer, both arm64 packages work. Bullseye has no Qt 6, so use the normal arm64 package there.

The packages without `system-qt` bring their own Qt, but they still depend on the system's graphics, font and X11 libraries, because the one Throne program also contains the desktop app. The package manager installs those libraries. Nothing needs a display.

Install the package and start the service:

```bash
sudo apt install ./Throne-<version>-debian-amd64.deb   # or: sudo dnf install ./Throne-<version>-fedora-amd64.rpm
sudo /opt/Throne/Throne --check                        # optional: can this computer run it?
sudo systemctl enable --now throne
```

- The package never enables or starts the unit by itself. An upgrade restarts the unit when it runs, and removing the package stops and disables it.
- `/var/lib/throne` (profiles, settings and the API key) stays when you remove the package, even with `apt purge`. Delete it yourself to start over.
- `systemctl start throne` returns once Throne has started. See [systemd: readiness, status and watchdog](@/headless/monitoring.md#systemd).
- `/etc/throne/throne.env` has a commented example for each variable the unit can use. Uncomment a line and run `sudo systemctl restart throne`. Upgrades keep your edits. Never put an API key in this file; `THRONE_API_KEY_FILE` names a key file instead.
- Logs: `journalctl -u throne -f`.
- The packages also install `thronectl` as `/usr/bin/thronectl`, and a second unit, `throne-web.service`, for the [web UI](@/headless/web.md#systemd). It is not enabled either.

To change the command line, add a drop-in with `sudo systemctl edit throne`:

```ini
[Service]
ExecStart=
ExecStart=/opt/Throne/Throne --headless --data-dir=%S/throne --debug
```

The unit runs Throne as root. [Privileges on Linux](#linux-privileges) compares this with the other ways to run it.

## Other Linux installs {#other-linux}

The [install script](@/get_started/installation.md#linux-script) puts Throne in `/opt/Throne`, and the [portable ZIP](@/get_started/installation.md#linux-zip) goes wherever you unpack it. Neither installs a unit. Install the unit yourself:

```bash
curl -fsSLo throne.service https://raw.githubusercontent.com/throneproj/Throne/dev/dist/linux/throne.service
sudo install -m 644 throne.service /etc/systemd/system/throne.service
sudo systemctl daemon-reload
sudo systemctl enable --now throne
```

The unit runs `/opt/Throne/Throne`. If Throne is in another folder, change that path with a drop-in, as shown for the AUR package below. `/etc/throne/throne.env` is optional for this unit. To have the examples, save [`dist/linux/throne.env`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne.env) there.

The [AUR package](@/get_started/installation.md#aur) is maintained separately and installs Throne to `/usr/lib/throne`. If it does not ship a unit, install the one above and point it at that path with a drop-in (`sudo systemctl edit throne`):

```ini
[Service]
ExecStart=
ExecStart=/usr/lib/throne/Throne --headless --data-dir=%S/throne
```

## Privileges on Linux {#linux-privileges}

TUN and the [kill switch](@/advanced/kill_switch.md) need privileges. Profiles with an [Extra Core](@/advanced/chains.md#extra-core) need a normal user to run that core as, because Throne never runs an Extra Core as root. Choose how the unit runs:

| How the unit runs | TUN | Kill switch | Extra Core profiles | Good for |
| --- | --- | --- | --- | --- |
| As root (the unit as shipped) | Yes | Yes | No | Gateways and dedicated boxes |
| As `throne` with capabilities (`caps.conf`) | Yes | No (`unsupported`) | Yes | A proxy box without the kill switch |
| As `throne` with a setuid-root `ThroneCore` | Yes | Yes | Yes | Everything, without running Throne itself as root |
| As `throne` with nothing else | No | No | Yes | Proxy only (the mixed inbound) |

**Capabilities.** Download [`caps.conf`](https://github.com/throneproj/Throne/blob/dev/dist/linux/caps.conf), then:

```bash
sudo useradd --system --no-create-home --home-dir /var/lib/throne --shell /usr/sbin/nologin throne
sudo install -Dm644 caps.conf /etc/systemd/system/throne.service.d/caps.conf
sudo systemctl daemon-reload && sudo systemctl restart throne
```

systemd hands `/var/lib/throne` over to `throne` at the next start. `ThroneCore` must have no setuid bit and no file capabilities in this setup, because running such a file drops the capabilities that the unit grants.

**Setuid core.** Use a drop-in with only `User=throne` and `Group=throne` under `[Service]`, and make the core run as root:

```bash
sudo chown root:root /opt/Throne/ThroneCore && sudo chmod u+s /opt/Throne/ThroneCore
```

A package upgrade replaces `ThroneCore` without the setuid bit. On Debian and Ubuntu, this keeps the bit across upgrades:

```bash
sudo dpkg-statoverride --update --add root root 4755 /opt/Throne/ThroneCore
```

Do not add `NoNewPrivileges=`, or a hardening option that implies it, to the unit: it would stop the setuid core.

`thronectl status` shows what this Throne can do in `capabilities.tun` and `capabilities.kill_switch`. The core checks for itself whether it can open `/dev/net/tun` and has `CAP_NET_ADMIN`, so a root container without `NET_ADMIN` reports `false` instead of failing later.

## systemd user unit {#user-unit}

Without root, Throne runs as a user unit in your own session. Download [`throne-user.service`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne-user.service), then:

```bash
install -Dm644 throne-user.service ~/.config/systemd/user/throne.service
systemctl --user daemon-reload && systemctl --user enable --now throne
loginctl enable-linger "$USER"   # start at boot, without a login
```

- The unit uses the desktop app's data directory, `~/.config/Throne`, so it has the same profiles and settings. Quit the desktop app first. While the unit runs, the desktop app says that Throne is running in the background and exits. Run `systemctl --user stop throne` before you open the desktop app.
- TUN and the kill switch need a setuid-root core. The desktop app sets it up the first time it asks for privileges (see [Privileges](@/guides/tun_mode.md#privileges-linux)), and `Throne --check` prints the commands.
- `Throne --health`, `Throne --print-connection` and `Throne --check` find this Throne without options when you run them as your user.

## Docker {#docker}

The image `ghcr.io/throneproj/throne:<version>` (also `latest`) is built for `linux/amd64` and `linux/arm64`. It contains Throne in `/opt/Throne`, `thronectl` in `/usr/local/bin` and `tini` as init. It keeps its data in the volume `/var/lib/throne`, exits with code 75 for a restart and uses `Throne --health` as its health check.

Start from the [`compose.yaml`](https://github.com/throneproj/Throne/blob/dev/docker/compose.yaml) in the Throne repository. It uses the `latest` tag; put a release tag there to update on your own schedule.

```bash
curl -fsSLO https://raw.githubusercontent.com/throneproj/Throne/dev/docker/compose.yaml
docker compose up -d
docker compose exec throne thronectl --key-file /var/lib/throne/config/api.key status
```

The important parts of that file:

```yaml
services:
  throne:
    image: ghcr.io/throneproj/throne:latest   # or a release tag
    restart: unless-stopped
    stop_grace_period: 45s
    cap_add: [NET_ADMIN]                      # Tun and the kill switch
    devices: ["/dev/net/tun:/dev/net/tun"]    # Tun and the kill switch
    volumes: ["throne-data:/var/lib/throne"]
    ports: ["2080:2080"]                      # the mixed (HTTP and SOCKS) inbound
volumes:
  throne-data:
```

Notes:

- **The proxy port.** The mixed inbound listens on `127.0.0.1` by default, which a published port cannot reach. Run `thronectl settings set inbound_address=0.0.0.0` and start the profile again. Docker's published ports bypass host firewalls such as `ufw`: on a host that the internet can reach, publish only the LAN address, for example `"192.168.1.2:2080:2080"`.
- **TUN and the kill switch** need `cap_add: [NET_ADMIN]` and the device `/dev/net/tun`. Without them the container still serves the mixed inbound.
- **Host network.** With `network_mode: host` (and no `ports:`), TUN and the kill switch act on the whole host, and only one Throne on the host can run TUN. See [Running beside a desktop Throne](@/headless/_index.md#beside-desktop).
- **Stopping.** Keep `stop_grace_period: 45s`. Docker's default of 10 seconds can kill Throne before it has stopped its core. With `restart: unless-stopped`, Docker starts Throne again after every exit, even after `thronectl shutdown`; stop it with `docker compose stop`.
- **Health.** Docker only marks an unhealthy container. It does not restart it.
- **Limits.** Extra Core profiles do not run, because the container runs as root. In rootless Docker and other user-namespaced containers the kill switch fails with `unsupported` (see [How Throne stays reachable](@/headless/recovery.md#protection)).
- **Options.** Put variables under `environment:`, or add flags with `command:`, which Docker appends after `--headless`. Change the data directory only with `THRONE_DATA_DIR`, because the health check finds Throne through it.
- **thronectl without `--key-file`.** Add `THRONE_KEY_FILE: /var/lib/throne/config/api.key` under `environment:`, and `docker compose exec throne thronectl status` works as is. Throne itself ignores that variable.
- **Importing a file.** Pipe it in with `-T`: `docker compose exec -T throne thronectl import - < links.txt`.
- **The API from outside the container.** The API listens on the container's loopback. To reach it from the network, set `THRONE_API_LISTEN: "*:9095"` with a `THRONE_API_ALLOW` list and publish port 9095. Admin requests, such as imports and settings, then also need `THRONE_API_ADMIN_LAN: "1"`, because every request through a published port comes from outside the container. Behind Docker's NAT all clients share one address, so one client with an old key locks out the others for a minute.
- **Logs:** `docker compose logs -f throne`.
- **Web UI.** Set `THRONE_WEB_LISTEN: "*:9096"` and publish port 9096, and the container also serves the [web UI](@/headless/web.md#docker).

## macOS {#macos}

A LaunchDaemon runs Throne as root at boot. TUN and the kill switch work; Extra Core profiles do not. Move `Throne.app` to `/Applications` first, download [`moe.Throne.headless.plist`](https://github.com/throneproj/Throne/blob/dev/dist/macos/moe.Throne.headless.plist), then:

```bash
sudo mkdir -p /Library/Logs/Throne
sudo install -m 644 -o root -g wheel moe.Throne.headless.plist /Library/LaunchDaemons/
sudo xattr -dr com.apple.quarantine /Applications/Throne.app   # unsigned builds only
sudo launchctl bootstrap system /Library/LaunchDaemons/moe.Throne.headless.plist
```

- To stop and unload it: `sudo launchctl bootout system/moe.Throne.headless`.
- Its data is in `/Library/Application Support/Throne` and its output in `/Library/Logs/Throne/throne.log`.
- The plist runs the program file directly, so launchd follows the process. Never start headless Throne with `open`: that hands it to LaunchServices and returns at once.
- launchd starts Throne again 10 seconds after an exit with an error. It cannot leave out exit codes, so it also retries every 10 seconds the exits that a new start does not fix: another Throne using the data directory (code 3), a bad option (code 64) and a configuration error (code 78). Read the log when Throne keeps starting and exiting.
- To see how it is doing: `sudo /Applications/Throne.app/Contents/MacOS/Throne --health`.

## Windows service {#windows}

`Throne.exe --headless --service` runs as a native Windows service under the LocalSystem account, so TUN and the kill switch work.

1. Install Throne for all users with the installer, into `Program Files`. A service must not run a program that a normal user can replace, so do not use a per-user installation or a ZIP in your user folder.
2. Download [`install-service.bat`](https://github.com/throneproj/Throne/blob/dev/dist/windows/install-service.bat) and [`uninstall-service.bat`](https://github.com/throneproj/Throne/blob/dev/dist/windows/uninstall-service.bat).
3. From an elevated Command Prompt, run:

   ```bat
   install-service.bat
   ```

   For Throne in another folder, give the path: `install-service.bat "D:\Apps\Throne\Throne.exe"`.

The script creates the service `Throne`, starts it after boot (delayed start) and sets its recovery actions:

```bat
sc.exe failure Throne reset= 86400 actions= restart/3000/restart/10000/restart/60000
sc.exe failureflag Throne 1
```

- `failureflag 1` makes Windows apply the recovery actions when Throne exits with an error code, not only after a crash. That is how restart requests (code 75) and the watchdog exits bring Throne back. Each one counts as a failure: within a day, the first restart comes after 3 seconds, the second after 10, and later ones after 60.
- `sc.exe stop Throne` and `thronectl shutdown` stop it for good (code 0). When Windows shuts down, it gives Throne up to 45 seconds.
- After it stopped with an error, `sc.exe query Throne` shows `WIN32_EXIT_CODE : 1066`, and Throne's own exit code as `SERVICE_EXIT_CODE`.
- Its data is in `C:\ProgramData\Throne` and its log in `C:\ProgramData\Throne\config\logs\throne.log`.
- `uninstall-service.bat` stops and deletes the service. The data in `C:\ProgramData\Throne` stays.
- Stop the service before you update Throne.

A scheduled task in Task Scheduler is no substitute for the service: Windows stops a task with a hard kill, so Throne cannot stop its core cleanly, and it cannot restart a task depending on its exit code.

### The data folder {#windows-data-folder}

The service keeps `C:\ProgramData\Throne` for SYSTEM and Administrators only. It creates the folder that way and sets that access again at every start, also for the files in it. If the folder already exists but belongs to another user, lets other users write to it (a folder you create by hand under `C:\ProgramData` does), is a link or junction, or contains a file or folder that is a link or junction, the service exits with code 78 and names the problem in the log. Remove that folder and start the service again. A `--data-dir` that normal users can write to only gets a warning in the log.

### Commands from a prompt {#windows-prompt}

Run `Throne.exe --health`, `--print-connection`, `--check` and `thronectl` from an elevated prompt, and give the `Throne.exe` commands `--data-dir C:\ProgramData\Throne`. An administrator is not LocalSystem, so its default data directory is a different one. Only Administrators and SYSTEM can open the service's pipe and read its key file. From a prompt that is not elevated, `--health` logs `[Instance] Not talking to … access denied` and exits with code 2.

`Throne.exe` is a Windows GUI program, so the Command Prompt does not wait for it. Throne writes its output to the console of the program that started it. To wait for it and read its exit code:

```bat
start "" /wait "C:\Program Files\Throne\Throne.exe" --health --data-dir C:\ProgramData\Throne
echo %ERRORLEVEL%
```

```powershell
$p = Start-Process 'C:\Program Files\Throne\Throne.exe' -ArgumentList '--health', '--data-dir', 'C:\ProgramData\Throne' -Wait -PassThru
$p.ExitCode
```

`--print-connection` prints POSIX `export` lines. In PowerShell, set the values yourself:

```powershell
$env:THRONE_ADDR = '127.0.0.1:9095'
$env:THRONE_KEY_FILE = 'C:\ProgramData\Throne\config\api.key'
thronectl status
```

### Running from a console {#windows-console}

To test headless Throne without the service:

```bat
start "" /wait "C:\Program Files\Throne\Throne.exe" --headless
```

`Ctrl+C` stops it cleanly, and a second `Ctrl+C` kills the core at once. Closing the console window, `taskkill /PID <pid>` without `/F`, signing out and shutting down also stop it cleanly. Outside the service, the data directory is `%LOCALAPPDATA%\Throne` (`C:\ProgramData\Throne` when it runs as LocalSystem). A restart starts a new Throne process and exits with code 0, so `start /wait` returns while the new process keeps running.

+++
title = "Installation & Services"
description = "Install headless Throne as a systemd service, a systemd user unit, a Docker container, a macOS LaunchDaemon or a Windows service."
weight = 10
toc = true
+++

Install Throne as described in [Installation](@/get_started/installation.md), then let a service manager run it:

| Setup | Runs as | Data directory |
| --- | --- | --- |
| [Linux package (systemd unit)](#linux-packages) | root | `/var/lib/throne` |
| [systemd user unit](#user-unit), any Linux | you | `~/.config/Throne` |
| [Docker](#docker) | root in the container | the volume `/var/lib/throne` |
| [macOS LaunchDaemon](#macos) | root | `/Library/Application Support/Throne` |
| [Windows service](#windows) | LocalSystem | `C:\ProgramData\Throne` |

Then [connect thronectl](@/headless/thronectl.md#connect).

## Debian, Ubuntu, Raspberry Pi OS, Fedora, openSUSE {#linux-packages}

The `.deb` and `.rpm` packages from the [releases page](https://github.com/throneproj/Throne/releases) and the package repositories install Throne to `/opt/Throne`, the systemd unit `throne.service`, its environment file `/etc/throne/throne.env` and `thronectl`. [Linux](@/get_started/installation.md#linux) says which package fits which system; on a Raspberry Pi, use the 64-bit Raspberry Pi OS.

```bash
sudo apt install ./Throne-<version>-debian-amd64.deb   # or: sudo dnf install ./Throne-<version>-fedora-amd64.rpm
sudo /opt/Throne/Throne --check                        # optional: can this computer run it?
sudo systemctl enable --now throne
journalctl -u throne -f
```

- The package never enables the unit. An upgrade restarts it when it runs; removing the package stops and disables it.
- `/var/lib/throne` (profiles, settings and the API key) stays when you remove the package, even with `apt purge`.
- To set a variable, uncomment it in `/etc/throne/throne.env` and run `sudo systemctl restart throne`. Upgrades keep your edits. Never put an API key there; `THRONE_API_KEY_FILE` names a key file instead.
- To change the command line, add a drop-in with `sudo systemctl edit throne`:

  ```ini
  [Service]
  ExecStart=
  ExecStart=/opt/Throne/Throne --headless --data-dir=%S/throne --debug
  ```

- A second unit, `throne-web.service`, runs the [web UI](@/headless/web.md#systemd). It is not enabled either.

## Other Linux installs {#other-linux}

The [install script](@/get_started/installation.md#linux-script) and the [portable ZIP](@/get_started/installation.md#linux-zip) install no unit, and the [AUR package](@/get_started/installation.md#aur) may not ship one. Install the packaged unit:

```bash
curl -fsSLo throne.service https://raw.githubusercontent.com/throneproj/Throne/dev/dist/linux/throne.service
sudo install -m 644 throne.service /etc/systemd/system/throne.service
sudo systemctl daemon-reload && sudo systemctl enable --now throne
```

It runs `/opt/Throne/Throne`, where the install script puts Throne. For another folder, such as `/usr/lib/throne` of the AUR package, change the path with a drop-in (`sudo systemctl edit throne`):

```ini
[Service]
ExecStart=
ExecStart=/usr/lib/throne/Throne --headless --data-dir=%S/throne
```

For the variable examples, save [`dist/linux/throne.env`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne.env) as `/etc/throne/throne.env`.

## Privileges on Linux {#linux-privileges}

TUN and the [kill switch](@/advanced/kill_switch.md) need privileges. [Extra Core](@/advanced/chains.md#extra-core) profiles need a normal user, because Throne never runs an Extra Core as root. Choose how the unit runs:

| How the unit runs | TUN | Kill switch | Extra Core profiles |
| --- | --- | --- | --- |
| As root (the unit as shipped) | Yes | Yes | No |
| As `throne` with capabilities (`caps.conf`) | Yes | No (`unsupported`) | Yes |
| As `throne` with a setuid-root `ThroneCore` | Yes | Yes | Yes |
| As `throne` with nothing else | No | No | Yes |

**Capabilities.** Download [`caps.conf`](https://github.com/throneproj/Throne/blob/dev/dist/linux/caps.conf), then:

```bash
sudo useradd --system --no-create-home --home-dir /var/lib/throne --shell /usr/sbin/nologin throne
sudo install -Dm644 caps.conf /etc/systemd/system/throne.service.d/caps.conf
sudo systemctl daemon-reload && sudo systemctl restart throne
```

`ThroneCore` must then have no setuid bit and no file capabilities, because running such a file drops the capabilities that the unit grants.

**Setuid core.** Use a drop-in with only `User=throne` and `Group=throne` under `[Service]`, and make the core run as root:

```bash
sudo chown root:root /opt/Throne/ThroneCore && sudo chmod u+s /opt/Throne/ThroneCore
```

A package upgrade replaces `ThroneCore` without the setuid bit. On Debian and Ubuntu, `sudo dpkg-statoverride --update --add root root 4755 /opt/Throne/ThroneCore` keeps it. Do not add `NoNewPrivileges=`, or a hardening option that implies it, to the unit: it stops the setuid core.

`thronectl status` shows what this Throne can do in `capabilities.tun` and `capabilities.kill_switch`.

## systemd user unit {#user-unit}

Without root, Throne runs as a user unit in your own session. Download [`throne-user.service`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne-user.service), then:

```bash
install -Dm644 throne-user.service ~/.config/systemd/user/throne.service
systemctl --user daemon-reload && systemctl --user enable --now throne
loginctl enable-linger "$USER"   # start at boot, without a login
```

- It uses the desktop app's data directory, `~/.config/Throne`, with the same profiles and settings. Quit the desktop app first, and run `systemctl --user stop throne` before you open it again.
- TUN and the kill switch need a setuid-root core. The desktop app sets it up when it first asks for [privileges](@/guides/tun_mode.md#privileges-linux), and `Throne --check` prints the commands.
- Run as your user, `Throne --health`, `--print-connection` and `--check` find this Throne without options.

## Docker {#docker}

The image `ghcr.io/throneproj/throne` (a release tag, or `latest`) runs on `linux/amd64` and `linux/arm64` and has `thronectl` in `/usr/local/bin`. Start from the [`compose.yaml`](https://github.com/throneproj/Throne/blob/dev/docker/compose.yaml) in the Throne repository:

```bash
curl -fsSLO https://raw.githubusercontent.com/throneproj/Throne/dev/docker/compose.yaml
docker compose up -d
docker compose exec throne thronectl --key-file /var/lib/throne/config/api.key status
```

Its main parts:

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

- **Proxy port.** The mixed inbound listens on `127.0.0.1`, which a published port cannot reach: run `thronectl settings set inbound_address=0.0.0.0` and start the profile again. Published ports bypass host firewalls such as `ufw`; on a host that the internet reaches, publish only the LAN address (`"192.168.1.2:2080:2080"`).
- **Host network** (`network_mode: host`, no `ports:`): TUN and the kill switch act on the whole host, and only one Throne there can run TUN.
- **Stopping.** Keep `stop_grace_period: 45s`, or Docker may kill Throne before it has stopped its core. `restart: unless-stopped` starts Throne again even after `thronectl shutdown`; use `docker compose stop`.
- **Health.** The health check runs `Throne --health`. Docker only marks an unhealthy container; it does not restart it.
- **Limits.** No Extra Core profiles, because the container runs as root. In rootless Docker and other user-namespaced containers, the kill switch fails with `unsupported`.
- **Options.** Variables go under `environment:`, flags in `command:` (appended after `--headless`). Change the data directory only with `THRONE_DATA_DIR`, which the health check uses.
- **thronectl.** Set `THRONE_KEY_FILE: /var/lib/throne/config/api.key` under `environment:` to drop `--key-file`. Import a file with `docker compose exec -T throne thronectl import - < links.txt`.
- **API from the network.** Set `THRONE_API_LISTEN: "*:9095"` and `THRONE_API_ALLOW`, publish port 9095, and add `THRONE_API_ADMIN_LAN: "1"` for imports and settings, because requests through a published port come from outside the container. Behind Docker's NAT all clients share one address, so one client with an old key locks out the others for a minute.
- **Logs:** `docker compose logs -f throne`. Web UI: see [Docker](@/headless/web.md#docker).

## macOS {#macos}

A LaunchDaemon runs Throne as root at boot, with TUN and the kill switch but without Extra Core profiles. Move `Throne.app` to `/Applications`, download [`moe.Throne.headless.plist`](https://github.com/throneproj/Throne/blob/dev/dist/macos/moe.Throne.headless.plist), then:

```bash
sudo mkdir -p /Library/Logs/Throne
sudo install -m 644 -o root -g wheel moe.Throne.headless.plist /Library/LaunchDaemons/
sudo xattr -dr com.apple.quarantine /Applications/Throne.app   # unsigned builds only
sudo launchctl bootstrap system /Library/LaunchDaemons/moe.Throne.headless.plist
```

- Stop and unload it with `sudo launchctl bootout system/moe.Throne.headless`.
- The log is `/Library/Logs/Throne/throne.log`. For the state, run `sudo /Applications/Throne.app/Contents/MacOS/Throne --health`.
- Never start headless Throne with `open`: it returns at once, and launchd loses the process.
- launchd starts Throne again 10 seconds after every exit with an error, also after errors that a new start does not fix (codes 3, 64 and 78). When Throne keeps starting and exiting, read the log.

## Windows service {#windows}

`Throne.exe --headless --service` runs as a Windows service under LocalSystem, so TUN and the kill switch work.

1. Install Throne for all users with the installer, into `Program Files`. A service must not run a program that a normal user can replace, so do not use a per-user installation or a ZIP in your user folder.
2. Download [`install-service.bat`](https://github.com/throneproj/Throne/blob/dev/dist/windows/install-service.bat) and [`uninstall-service.bat`](https://github.com/throneproj/Throne/blob/dev/dist/windows/uninstall-service.bat).
3. From an elevated Command Prompt, run `install-service.bat`, or `install-service.bat "D:\Apps\Throne\Throne.exe"` for Throne in another folder.

The service `Throne` starts after boot (delayed start). Its recovery actions restart Throne after 3, 10 and then 60 seconds, also after an exit with an error code, such as a restart request (code 75) or a watchdog exit.

- `sc.exe stop Throne` and `thronectl shutdown` stop it for good. Stop the service before you update Throne.
- After an error, `sc.exe query Throne` shows Throne's exit code as `SERVICE_EXIT_CODE` (`WIN32_EXIT_CODE` is then `1066`). The log is `C:\ProgramData\Throne\config\logs\throne.log`.
- `uninstall-service.bat` removes the service and keeps the data.

### The data folder {#windows-data-folder}

The service keeps `C:\ProgramData\Throne` for SYSTEM and Administrators only. If the folder belongs to another user, lets other users write to it (as a folder you create by hand under `C:\ProgramData` does), or is or contains a link or junction, the service exits with code 78 and names the problem in the log. Delete the folder and start the service again.

### Commands from a prompt {#windows-prompt}

Run `Throne.exe --health`, `--print-connection`, `--check` and `thronectl` from an elevated prompt, and give the `Throne.exe` commands `--data-dir C:\ProgramData\Throne`: an administrator is not LocalSystem and has another default data directory. Only Administrators and SYSTEM can open the service's pipe and read its key file.

`Throne.exe` is a GUI program, so the Command Prompt does not wait for it. To wait and read its exit code:

```bat
start "" /wait "C:\Program Files\Throne\Throne.exe" --health --data-dir C:\ProgramData\Throne
echo %ERRORLEVEL%
```

`--print-connection` prints POSIX `export` lines. In PowerShell, set the values yourself:

```powershell
$env:THRONE_ADDR = '127.0.0.1:9095'
$env:THRONE_KEY_FILE = 'C:\ProgramData\Throne\config\api.key'
thronectl status
```

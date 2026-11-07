+++
title = "Managing with thronectl"
description = "Connect thronectl to headless Throne, add profiles and subscriptions, move a desktop setup over and manage Throne from another device."
weight = 20
toc = true
+++

Headless Throne has no window. You manage it with `thronectl`, the command-line client of the [Throne API](@/advanced/api.md), or from a browser with the [web UI](@/headless/web.md).

## Connect thronectl to Throne {#connect}

The `.deb` and `.rpm` packages install `thronectl` as `/usr/bin/thronectl`, the Linux ZIP has it next to `Throne`, and the Docker image has it in `/usr/local/bin`. On macOS, Windows and other computers you manage Throne from, [install thronectl](@/advanced/api.md#install-thronectl) yourself.

`thronectl` reads Throne's address from `THRONE_ADDR` (default `127.0.0.1:9095`) and the key from the file that `THRONE_KEY_FILE` names, from `THRONE_KEY` or from `--key-file`. It never takes the key itself as an argument. `Throne --print-connection` prints both variables. Run it as the user Throne runs as, with the same `--data-dir`:

```bash
$ sudo /opt/Throne/Throne --print-connection
export THRONE_ADDR=127.0.0.1:9095
export THRONE_KEY_FILE=/var/lib/throne/config/api.key
```

- **systemd user unit:** `eval "$(/opt/Throne/Throne --print-connection)"`, then `thronectl status`.
- **Throne runs as root** (system unit, macOS LaunchDaemon): only root can read the key file, and `sudo` drops the variables. Copy the key to a file of your own:

  ```bash
  mkdir -p ~/.config/throne
  sudo install -m 600 -o "$USER" /var/lib/throne/config/api.key ~/.config/throne/api-key
  export THRONE_KEY_FILE=~/.config/throne/api-key
  thronectl status
  ```

  Or run `thronectl` as root: `sudo thronectl --key-file /var/lib/throne/config/api.key status`.
- **Docker:** `docker compose exec throne thronectl --key-file /var/lib/throne/config/api.key status`.
- **Windows service:** see [Commands from a prompt](@/headless/installation.md#windows-prompt).

Put the `export` line in your shell's startup file, such as `~/.bashrc`. To complete commands and IDs in the shell, see [Shell completion](@/advanced/api.md#completion).

## Everyday commands {#profiles}

```bash
thronectl status
thronectl import links.txt               # share links, a subscription, Clash, sing-box, Xray, WireGuard or OpenVPN
thronectl sub add 'https://sub.example.com/abc' --name Provider
thronectl profiles --group 3
thronectl connect 12                     # or: thronectl connect best --group 3
thronectl tun on
thronectl routes                         # then: thronectl route <id>
thronectl logs -f
thronectl disconnect
```

- `import` reads a file of up to 900 KiB, or standard input with `-`; `--url URL` makes Throne download a URL once. Without `--group`, profiles go into the group that Throne shows.
- `sub add --route ID` gives the new group its own [routing profile](@/guides/routing.md#group-routing); `--import-route` uses the one the subscription provides.
- `connect best` starts the profile with the lowest latency, after testing the ones without a recent result (`--max-age 1h`).
- Tests can take minutes, so give them a longer timeout: `thronectl --timeout 5m test latency --group 3`.
- `thronectl restart` restarts Throne as its [restart mode](@/headless/command_line.md#restart-modes) says. `thronectl shutdown` makes it exit with code 0: systemd, launchd and the Windows service leave it stopped, but Docker with `restart: unless-stopped` starts it again.

Add `--json` for JSON output; `thronectl help <command>` describes a command. Every command and exit code: [Commands](@/advanced/api.md#commands).

## Moving a desktop setup {#from-desktop}

OTP accounts, VPN sign-ins and routing profiles are easier to set up in a desktop Throne. Set them up there, then move them over with a backup:

```bash
# On the desktop: make a backup in the Backup and Restore settings, or, with the desktop's Throne API on:
thronectl backup create -o desktop.thrbackup
scp desktop.thrbackup box:

# On the box:
thronectl backup restore desktop.thrbackup
```

- `backup restore` shows what the archive holds and asks first. Without a terminal, add `--yes`. Throne restarts after the restore.
- By default it restores profiles, routing profiles, OTP accounts and IP lists. To restore settings too, add `--parts profiles,routes,otp,ip_lists,settings`. A settings restore keeps the settings that belong to this device: the Throne API, the inbound address and ports, TUN, the kill switch, NTP, logging and file paths.
- Restore `profiles` and `routes` together. Restored alone, the groups lose their own routing profiles.
- Throne refuses archives with profiles that would run programs or read files on its device, such as Extra Core profiles (`invalid_archive`).
- Backups hold your profiles' credentials and OTP secrets in plain text. Delete them when you are done.

For backups in the desktop app, see [Back up and restore](@/guides/backup.md#backup-restore).

## Settings {#settings}

```bash
thronectl settings
thronectl settings set kill_switch=true log_level=debug
```

`thronectl settings` lists the settings that the API manages, with their current values; [Settings](@/advanced/api.md#settings) explains them. The others can only be changed in a desktop Throne: move them over with a [settings restore](#from-desktop).

## Managing from another device {#remote}

The API listens only on `127.0.0.1`. To manage Throne from another device, let it listen on the network with an allow list. For the systemd unit, put this in `/etc/throne/throne.env` and run `sudo systemctl restart throne`:

```bash
THRONE_API_LISTEN=*:9095
THRONE_API_ALLOW=192.168.1.0/24
THRONE_API_ADMIN_LAN=1
```

On the other device, put the key in a file that only you can read, and set `THRONE_KEY_FILE` to that file and `THRONE_ADDR` to `<box>:9095`.

- **Admin requests**, which import, add subscriptions, delete, change settings, back up, read the log, restart and shut down, are answered only from Throne's own device unless `THRONE_API_ADMIN_LAN=1` (`--api-admin-lan`) is set. Otherwise they fail with `forbidden`. Leave it out if other devices should only monitor and switch profiles.
- After 5 failed authentications within a minute from one address, Throne refuses that address for 60 seconds. A client with an old key can lock out the other clients behind the same NAT.
- The [kill switch](@/advanced/kill_switch.md) always lets the API's replies out. On Linux, TUN can take the replies to remote clients; see [How Throne stays reachable](@/headless/recovery.md#protection).
- Traffic to the API is encrypted and authenticated. Still, keep the API on loopback unless you need it on the network, and always set an allow list.

+++
title = "Managing with thronectl"
description = "Connect thronectl to headless Throne, add profiles and subscriptions, move a desktop setup over with a backup, change settings and manage Throne from another device."
weight = 20
toc = true
+++

Headless Throne has no window, so you manage it with `thronectl`, the command-line client of the [Throne API](@/advanced/api.md). This page shows how to connect `thronectl` to headless Throne and how to set Throne up with it. [Throne API & thronectl](@/advanced/api.md) has the full list of commands and flags. To manage Throne from a browser instead, see [Web UI](@/headless/web.md).

## Install thronectl {#install-thronectl}

The `.deb` and `.rpm` packages of Throne install `thronectl` as `/usr/bin/thronectl`, and the Linux ZIP has it next to `Throne`. Elsewhere, install it on the computer that runs Throne, and on any other computer you want to manage Throne from:

- Download a binary for Linux, Windows, macOS or FreeBSD from the [Throne-api releases](https://github.com/throneproj/Throne-api/releases), or
- build it with Go: `go install github.com/throneproj/Throne-api/cmd/thronectl@latest`.

The [Docker image](@/headless/installation.md#docker) already contains `thronectl` in `/usr/local/bin`. The Windows and macOS downloads of Throne do not include it.

To have the shell complete commands, profile IDs and setting names, see [Shell completion](@/advanced/api.md#completion).

## Connect thronectl to Throne {#connect}

`thronectl` reads Throne's address from `THRONE_ADDR` (default `127.0.0.1:9095`) and the key from the file that `THRONE_KEY_FILE` names, or from `THRONE_KEY`, or from `--key-file`. It never takes the key itself as an argument.

`Throne --print-connection` prints both variables as `export` lines, never the key itself. Run it as the user Throne runs as, and with the same `--data-dir`:

```bash
$ sudo /opt/Throne/Throne --print-connection
export THRONE_ADDR=127.0.0.1:9095
export THRONE_KEY_FILE=/var/lib/throne/config/api.key
```

How to use it depends on how Throne runs:

- **systemd user unit** (Throne runs as you): `eval "$(/opt/Throne/Throne --print-connection)"` sets both variables. Then run `thronectl status`.
- **System unit, Docker or macOS LaunchDaemon** (Throne runs as root): the key file is readable only by root, and `sudo` drops the variables. Either copy the key to a file of your own:

  ```bash
  mkdir -p ~/.config/throne
  sudo install -m 600 -o "$USER" /var/lib/throne/config/api.key ~/.config/throne/api-key
  export THRONE_KEY_FILE=~/.config/throne/api-key
  thronectl status
  ```

  or run `thronectl` as root with the key file: `sudo thronectl --key-file /var/lib/throne/config/api.key status`.
- **Docker:** run `thronectl` inside the container: `docker compose exec throne thronectl --key-file /var/lib/throne/config/api.key status`.
- **Windows service:** see [Commands from a prompt](@/headless/installation.md#windows-prompt).

Put the `export` line in your shell's startup file (for example `~/.bashrc`) so that every new shell has it.

`--print-connection` exits with code 2 when the API is off or not listening, or when its key is not in a file. The last case happens when the data directory comes from a desktop Throne that turned the API on with its own key. Use that key as `THRONE_KEY` (in the desktop app: `Integration Settings` → `Throne API` → `Copy for scripts`), or start Throne with `--api-key-file`.

## Admin requests {#admin-requests}

Requests that change what Throne has are admin requests: `import`, `sub add`, `group`, `profile rm`, `settings set`, `backup`, `restart` and `shutdown`. So is `logs`, because the log names the sites that connections went to. Throne answers them only from its own device, unless it runs with `--api-admin-lan` (`THRONE_API_ADMIN_LAN=1`). From another device they fail with `forbidden`.

All other requests, such as `status`, `connect`, `tun on` or `test`, work from every device that may reach the API and has the key.

## Profiles and subscriptions {#profiles}

```bash
thronectl import links.txt                        # share links, a subscription body, Clash, sing-box, Xray, WireGuard or OpenVPN
thronectl sub add 'https://sub.example.com/abc' --name Provider
thronectl groups
thronectl profiles --group 3
thronectl connect 12                              # or: thronectl connect best --group 3
thronectl tun on
```

- `thronectl import` reads a file of up to 900 KiB, or standard input with `-`. `--url URL` makes Throne download a URL once. Without `--group`, profiles go into the group that is current in Throne.
- `thronectl sub add` adds a subscription group, named after the URL's host unless you give `--name`, and downloads it. It updates automatically unless you add `--no-auto-update`.
- `thronectl sub add --route ID` gives the new group its own routing profile, with an ID from `thronectl routes`. `--import-route` keeps the routing profile that the subscription provides and refreshes it at every update. See [Routing profile per group](@/guides/routing.md#group-routing). `thronectl groups` shows each group's routing profile under `ROUTE`.
- `thronectl connect best` starts the profile with the lowest latency, and first tests the profiles without a recent result (`--max-age`, for example `--max-age 1h`).
- `thronectl test latency --group 3` tests a group. Tests can take minutes; give them a longer `--timeout`, for example `thronectl --timeout 5m test latency --group 3`.

The running profile starts again at every start of Throne, with TUN if TUN was on, until you run `thronectl disconnect`. To start without it once, use [`--no-resume`](@/headless/command_line.md#options).

## Moving a desktop setup {#from-desktop}

Some things are easier to set up in a desktop Throne, such as routing profiles, OTP accounts or VPN sign-ins. Set them up there, then move them over with a backup:

```bash
# On the desktop: make a backup in the Backup and Restore settings, or, with the desktop's Throne API on:
thronectl backup create -o desktop.thrbackup
scp desktop.thrbackup box:

# On the box:
thronectl backup restore desktop.thrbackup
```

- `backup restore` shows what is in the archive and asks before it restores. Without a terminal, add `--yes`.
- By default it restores profiles, routing profiles, OTP accounts and IP lists. To restore settings too, add `--parts profiles,routes,otp,ip_lists,settings`.
- Restore `profiles` and `routes` together, as the default does. If you restore only one of them, the groups lose their own routing profiles and follow the active one.
- A settings restore keeps the settings that belong to this device: the Throne API, the inbound address and ports, TUN, the kill switch, NTP, logging and file paths.
- Throne restarts after a restore.
- Through the API, Throne refuses archives with profiles that would run programs or read files on its device, such as Extra Core profiles (`invalid_archive`).
- Backup archives hold your profiles' credentials and OTP secrets in plain text. Delete them when you are done.

See [Back up and restore](@/guides/backup.md#backup-restore) for backups in the desktop app.

## Settings {#settings}

`thronectl settings` lists the settings that the API can change, with their current values. `thronectl settings set` changes one or more:

```bash
thronectl settings
thronectl settings get log_level
thronectl settings set kill_switch=true log_level=debug
```

`true`, `false` and numbers are sent as such, anything else as text. The settings include the ones that `Throne --check` and a `tun_conflict` error name: TUN's address ranges (`tun_ipv4_cidr`, `tun_ipv6_cidr`), the core's DNS port (`dns_in_port`), the inbound (`inbound_address`, `inbound_port`), the Clash API (`clash_api_port`, `clash_listen_addr`) and the sing-box API (`singbox_api_port`). Some settings apply only when the profile starts again; the [settings table](https://github.com/throneproj/Throne-api/blob/main/spec/api.md#settings) in the API specification says which.

`thronectl settings set dns_in_lan=true` lets other devices on the network use the core's DNS server, for example a router; see [DNS server for other devices](@/guides/dns.md#dns-server).

Settings that the API does not manage can only be changed in a desktop Throne. Move them over with a [settings restore](#from-desktop).

## Managing from another device {#remote}

By default the API listens only on `127.0.0.1`. To manage Throne from another device, let it listen on the network with an allow list, and allow admin requests when that device should import and change settings. For the systemd unit, put this in `/etc/throne/throne.env` and run `sudo systemctl restart throne`:

```bash
THRONE_API_LISTEN=*:9095
THRONE_API_ALLOW=192.168.1.0/24
THRONE_API_ADMIN_LAN=1
```

On the other device, copy the key into a file that only you can read, set `THRONE_KEY_FILE` to it and set `THRONE_ADDR=<box>:9095`.

- The [kill switch](@/advanced/kill_switch.md) always lets the API's replies out, so clients reach the API wherever they connect from.
- Traffic to the API is encrypted and authenticated, also on the LAN. Still, keep the API on loopback unless you need it on the network, and always give an allow list.
- After 5 failed authentications within a minute from one address, Throne refuses that address for 60 seconds. A client with an old key can lock out other clients behind the same NAT.
- On Linux, when TUN starts while the API listens on a network address, Throne warns once if remote clients may lose the API. See [How Throne stays reachable](@/headless/recovery.md#protection).

## Commands {#commands}

The commands you need most:

| Command | What it does |
| --- | --- |
| `thronectl status` | State, running profile, TUN, routing profile, version, capabilities, and a TUN conflict if there is one |
| `thronectl groups`, `thronectl profiles [--group N]` | Groups and profiles with their IDs and last test results |
| `thronectl connect <id>`, `connect best`, `connect last` | Start a profile |
| `thronectl disconnect`, `thronectl toggle` | Stop the profile; connect or disconnect |
| `thronectl tun on`, `tun off` | TUN mode |
| `thronectl routes`, `thronectl route <id>` | List and activate routing profiles |
| `thronectl test latency \| ip \| speed --group N` | Test profiles |
| `thronectl sub update --all` | Update all subscriptions |
| `thronectl geo update`, `thronectl rulesets update` | Download the geo files and rule-sets again |
| `thronectl traffic --by app --since 7d` | Traffic totals |
| `thronectl logs -n 50`, `thronectl logs -f` | The last lines of Throne's log; with `-f`, then new lines as Throne writes them. See [Logs](@/headless/monitoring.md#logs) |
| `thronectl restart`, `thronectl shutdown` | Restart Throne; make it exit |

Add `--json` to any command for JSON output, which suits scripts. `thronectl help <command>` describes a command. For every command and flag, and for the exit codes of `thronectl`, see [Throne API & thronectl](@/advanced/api.md#commands).

`thronectl restart`, a backup restore and `SIGHUP` restart Throne; how depends on the [restart mode](@/headless/command_line.md#restart-modes). `thronectl shutdown` makes Throne exit with code 0. Under systemd, launchd and the Windows service it then stays down; Docker with `restart: unless-stopped` starts it again.

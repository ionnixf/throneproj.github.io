+++
title = "Throne API & thronectl"
description = "Control and manage Throne from scripts, hotkeys and other devices with the Throne API and its command-line client thronectl."
weight = 80
toc = true
+++

The Throne API lets scripts, hotkey tools and other devices control and manage Throne desktop. `thronectl` is its command-line client, and `thronectl web` serves a [web UI](@/headless/web.md). To write your own client, use the Go library and the [API specification](https://github.com/throneproj/Throne-api/blob/main/spec/api.md) in the [Throne-api](https://github.com/throneproj/Throne-api) repository.

[Headless Throne](@/headless/_index.md) has the API on from the start, see [Managing with thronectl](@/headless/thronectl.md). Throne for Android has no API.

## Turn on the API in Throne {#enable}

1. Open `Settings` → `Integration Settings` → `Throne API` (Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)).
2. Tick `Enable the Throne API`. Throne fills in a new key.
3. Click `OK`.

The API listens on `127.0.0.1:9095`, so only programs on this computer can connect. To let other devices connect, set `Listen on` to `Local network` and list them in `Allowed addresses`, such as `192.168.1.0/24, 10.0.0.5` (empty allows every address). Tick `Allow management from the local network` only if they should also send [admin requests](#admin-requests).

`Copy for scripts` copies the `THRONE_ADDR` and `THRONE_KEY` settings that `thronectl` reads. `Regenerate` makes a new key; scripts with the old one stop working. The [kill switch](@/advanced/kill_switch.md) does not block API clients.

## Install thronectl {#install-thronectl}

The Linux `.deb`, `.rpm` and ZIP downloads of Throne and the headless Docker image include `thronectl`. Otherwise, download it for Linux, Windows, macOS or FreeBSD from the [Throne-api releases](https://github.com/throneproj/Throne-api/releases) and put it in a folder on your `PATH`, or install it with Go:

```bash
go install github.com/throneproj/Throne-api/cmd/thronectl@latest
```

## Connect thronectl to Throne {#connect}

`thronectl` reads the address from `THRONE_ADDR` (default `127.0.0.1:9095`) and the key from `THRONE_KEY`, or from the file that `THRONE_KEY_FILE` names. It never takes the key as an argument, so keep it in a file that only you can read:

1. In Throne, click `Copy for scripts`.
2. Save only the key, without `THRONE_KEY=`, in a file such as `~/.config/throne/api-key`. On Linux and macOS, `chmod 600` it.
3. Set the variables, for example in `~/.bashrc`:

   ```bash
   export THRONE_ADDR=127.0.0.1:9095
   export THRONE_KEY_FILE=~/.config/throne/api-key
   ```

   In PowerShell (`$PROFILE`):

   ```powershell
   $env:THRONE_ADDR = "127.0.0.1:9095"
   $env:THRONE_KEY_FILE = "$HOME\throne-api-key.txt"
   ```

4. Test it with `thronectl status`.

Flags, before or after the command: `--addr host:port` and `--key-file file` override the variables, `--json` prints JSON instead of a table, and `--timeout 30s` sets how long to wait (`0` waits as long as Throne needs). Under systemd, `thronectl` also reads the key from the credential `api.key` (`LoadCredential=api.key:/path/to/key`). For headless Throne, see [Connect thronectl to Throne](@/headless/thronectl.md#connect).

## Commands {#commands}

`thronectl help <command>` describes a command. Commands take the IDs that the lists show.

| Command | What it does |
| --- | --- |
| `status` | State, running profile, modes, routing profile, Throne version, and what else holds TUN, if anything. |
| `groups` | Lists the groups, with their own routing profiles. |
| `profiles [--group N] [--name REGEX] [--ids 1,2]` | Lists profiles with their last test results. |
| `connect <id>` | Starts a profile. `--restart` restarts it if it already runs. |
| `connect last` | Starts the running profile, else the last one, else the remembered one. |
| `connect best [--group N] [--name REGEX] [--max-age 1h]` | Starts the profile with the lowest latency, first testing those without a result newer than `--max-age`. |
| `disconnect`, `toggle` | Stops the running profile; `toggle` runs `connect last` when nothing runs. |
| `tun on`, `tun off`, `tun toggle` | TUN mode. |
| `proxy on`, `proxy off`, `proxy toggle` | The system proxy. |
| `routes`, `route <id>` | Lists the routing profiles; activates one and restarts the running profile. |
| `test latency`, `test ip`, `test speed` | Runs a URL, IP or speed test on `--group N` (optionally with `--name REGEX`) or on profile IDs. |
| `jobs`, `job <id>`, `cancel <id>` | Lists the recent tests, shows one, stops one. |
| `clear` | Clears test results, selected as for `test`. |
| `live`, `traffic [--by profile] [--by app] [--since 24h]` | Current traffic rates; traffic totals. `--since` also takes days, such as `7d`. |
| `logs [-n N] [-f]` | The last `N` lines of the log (default 200, at most 1000). `-f` follows new lines. |
| `import [--group N] FILE` | Imports links, a subscription or a Clash, sing-box, Xray, WireGuard or OpenVPN config of up to 900 KiB. `-` reads standard input; `--url URL` makes Throne download a URL once. |
| `sub add <url> [--name NAME]` | Adds a subscription group and updates it. Options: `--no-auto-update`, `--no-update`, `--route ID` (own routing profile), `--import-route` (keep the subscription's routing profile). |
| `sub update <id>…`, `sub update --all` | Updates subscription groups, or all that are not archived. |
| `group add <name>`, `group rm <id>` | Adds a group; deletes a group with its profiles. |
| `profile rm <id>…`, `profile rm --group N [--name REGEX]` | Deletes profiles. |
| `settings [get] [KEY…]`, `settings set KEY=VALUE…` | Shows or changes [settings](#settings). |
| `backup create [-o FILE] [--parts LIST]` | Writes a `.thrbackup` archive. |
| `backup restore FILE [--parts LIST] [--yes]` | Shows an archive, asks, restores it and restarts Throne. Default parts: `profiles,routes,otp,ip_lists`. |
| `rulesets update` | Downloads the running profile's remote rule-sets again. |
| `geo`, `geo update [geoip] [geosite] [--only-missing]` | Shows the Xray geo files; downloads them again. |
| `restart`, `shutdown [--yes]` | Restarts Throne; quits it after asking. |
| `web [--listen ADDR] …`, `web passwd` | Serves the [web UI](@/headless/web.md); sets its password. |
| `keygen [-o FILE]` | Creates a new key, printed or written to a new file that only you can read. It does not connect to Throne. |
| `call <METHOD> <PATH> [key=value…] [--body JSON]` | Sends any request of the [API specification](https://github.com/throneproj/Throne-api/blob/main/spec/api.md). |
| `completion bash`, `zsh`, `fish` or `powershell` | Prints a [completion](#completion) script. |
| `version` | Prints the version of `thronectl`. |

- Without `--group`, `connect best` and `import` use the group Throne shows.
- `--name` is a case-insensitive regular expression that may match any part of the name. Quote it: `--name 'DE|NL'`.
- `--parts` takes a comma-separated list of `profiles`, `routes`, `settings`, `otp`, `ip_lists` and `icons`.
- Commands wait for the result up to `--timeout`; Throne finishes on its own if it needs longer. `--no-wait` returns at once. Give tests and geo downloads more time, such as `--timeout 15m`. Throne runs one test at a time.
- `route` does not change groups that have their own [routing profile](@/guides/routing.md#group-routing). Through the API, a group gets one only from `sub add`; change it later in Throne's group options.
- Exit codes: `0` success; `1` an error from Throne (`error: <code>: <message>`), a failed update, or a question answered with no; `2` a wrong command or flag; `3` no connection, a rejected key, or an unreadable file.

### Settings {#settings}

`thronectl settings set log_level=debug kill_switch=true` changes settings. `true`, `false` and numbers are sent as such, anything else as text. If one value is wrong, Throne changes nothing.

| Area | Settings |
| --- | --- |
| Start | `remember_enable` |
| Logs | `log_level`, `xray_log_level` |
| Mixed inbound | `inbound_enabled`, `inbound_address`, `inbound_port` |
| Tests | `test_url`, `test_concurrency`, `test_timeout_ms` |
| Automatic updates | `sub_auto_update`, `sub_respect_server_interval`, `route_auto_update` (minutes, `0` is off) |
| Kill switch | `kill_switch` |
| TUN | `tun_strict_route`, `tun_auto_redirect` (Linux), `tun_mtu`, `tun_ipv6`, `tun_ipv4_cidr`, `tun_ipv6_cidr` |
| Ports and APIs | `dns_in_port`, `dns_in_lan` ([DNS server for other devices](@/guides/dns.md#dns-server)), `clash_api_port`, `clash_listen_addr`, `singbox_api_port` |
| DNS and time | `remote_dns`, `direct_dns`, `ntp_enabled`, `ntp_server`, `ntp_interval` |

Most changes apply when the running profile restarts (TUN settings, when TUN mode starts again). `thronectl status` says when a restart is needed; `thronectl connect last --restart` does it. Allowed values: see the [settings table](https://github.com/throneproj/Throne-api/blob/main/spec/api.md#settings) of the specification.

## Admin requests {#admin-requests}

`logs`, `import`, `sub add`, `group add`, `group rm`, `profile rm`, `settings set`, `backup`, `restart` and `shutdown` send admin requests (`logs` because the log names the sites you visited). The other commands send control requests, with the same key.

Throne answers admin requests only from its own computer (`127.0.0.1` or `::1`). From other devices they fail with `forbidden`, unless `Allow management from the local network` is on (`--api-admin-lan` for headless Throne).

- Backups contain your profiles' passwords and keys and your OTP secrets in plain text. `thronectl` makes them readable only by you; keep it that way and delete them when done.
- `backup restore` refuses archives with profiles that run programs or read files, such as Extra Core profiles (`invalid_archive`). Restore those in the Throne window.
- `backup restore` and `shutdown` ask first. In scripts without a terminal, add `--yes`.
- Restoring `settings` keeps this computer's own settings, such as the Throne API, ports, TUN and the kill switch.

## Examples {#examples}

```bash
thronectl profiles --group 3 --name 'DE|NL'
thronectl connect 12
thronectl --timeout 2m test latency --group 3
thronectl sub add 'https://sub.example.com/abc' --name Provider
thronectl connect best --max-age 10m   # retest results older than 10 minutes
```

Speed-test the profiles of group 3 whose names contain `DE` or `NL`, then connect the fastest (needs [jq](https://jqlang.org/)):

```bash
best=$(thronectl --json --timeout 15m test speed --group 3 --name 'DE|NL' |
  jq -r '[.results[] | select(.download_bps > 0)] | max_by(.download_bps) | .id // empty')
[ -n "$best" ] && thronectl connect "$best"
```

In PowerShell:

```powershell
$job = thronectl --json --timeout 15m test speed --group 3 --name 'DE|NL' | ConvertFrom-Json
$best = $job.results | Where-Object download_bps -gt 0 | Sort-Object download_bps -Descending | Select-Object -First 1
if ($best) { thronectl connect $best.id }
```

### One key to connect and disconnect {#example-hotkey}

Let a Stream Deck, macro pad or hotkey tool run `thronectl toggle`. Such tools often don't read your shell's settings: set `THRONE_KEY_FILE` (and `THRONE_ADDR`, if needed) as a user environment variable, or add `--key-file`. Throne also has [global hotkeys](@/reference/shortcuts.md#global-hotkeys) for `Start/Stop Profile` and `Toggle Tun Mode`.

## Shell completion {#completion}

`thronectl completion <shell>` prints a script that completes commands, flags and setting names, and, when Throne answers within a second, the IDs of profiles, groups, routing profiles and tests.

```bash
# bash: in ~/.bashrc (on macOS, ~/.bash_profile)
eval "$(thronectl completion bash)"
# zsh: in ~/.zshrc
autoload -Uz compinit && compinit
source <(thronectl completion zsh)
# fish
thronectl completion fish > ~/.config/fish/completions/thronectl.fish
```

```powershell
# In $PROFILE
thronectl completion powershell | Out-String | Invoke-Expression
```

## Security {#security}

- The key controls Throne's connection, modes and routing, and reads your profiles' names, addresses, test results and traffic. Where [admin requests](#admin-requests) are allowed, it also reads profile configurations and backups, which contain your credentials in plain text.
- Keep the key out of command lines and scripts that others can read: use a key file only you can read, or `THRONE_KEY` from a secret store.
- Traffic between `thronectl` and Throne is encrypted and authenticated with the key, but without forward secrecy: whoever learns the key can read recorded traffic.
- If the key leaks, click `Regenerate` and give your scripts the new key. For headless Throne, see [Lockout Protection & Recovery](@/headless/recovery.md).
- Keep `Listen on` at `This computer only` unless you need the API on the network.
- After 5 failed attempts within a minute, Throne refuses that address for 60 seconds, also for other clients behind the same router. It never refuses `127.0.0.1`, or an address that logged in during the last 10 minutes.
- Throne logs every request that changes something, with the client's address.

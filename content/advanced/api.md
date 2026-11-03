+++
title = "Throne API & thronectl"
description = "Control and manage Throne from scripts, hotkeys and other devices with the Throne API and its command-line client thronectl."
weight = 80
toc = true
+++

The Throne API (since 1.4.0) lets scripts, hotkey tools and other devices control Throne: start and stop profiles, switch TUN mode and the system proxy, change the routing profile, run tests, and read profiles and traffic statistics. It can also manage Throne: import profiles, add and update subscriptions, delete groups and profiles, change settings, back up and restore, and restart or quit Throne.

`thronectl` is the command-line client of the API; `thronectl web` also serves a [web UI](@/headless/web.md) for the browser. This page explains how to turn the API on in Throne desktop, how to install and connect `thronectl`, and what each command does.

[Headless Throne](@/headless/_index.md), which runs without a window on a server or in a container, has the API on from its first start, and you manage it with `thronectl`. [Managing with thronectl](@/headless/thronectl.md) shows how to connect to it and set it up.

Throne for Android has no Throne API.

## Turn on the API in Throne {#enable}

The API is off by default.

1. Open `Settings` → `Integration Settings` (Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)), then the `Throne API` tab.
2. Tick `Enable the Throne API`. Throne fills in a new key.
3. Click `OK`. `Status` shows `Listening on 127.0.0.1:9095`.

The tab has these controls:

| Control | What it does |
| --- | --- |
| `Enable the Throne API` | Turns the API on. |
| `Listen on` | `This computer only` (default) listens on `127.0.0.1`, so only programs on this computer can connect. `Local network` listens on every address of the computer, so other devices can connect too. |
| `Port` | The TCP port, `9095` by default. |
| `Allowed addresses` | Only with `Local network`: the IP addresses and ranges that may connect, such as `192.168.1.0/24, 10.0.0.5`, separated by commas or spaces. Empty lets every address connect. Programs on this computer can always connect through `127.0.0.1`. |
| `Allow management from the local network` | Only with `Local network`: other devices may also send [admin requests](#admin-requests), such as imports, settings changes and backups. Programs on this computer can always send them. |
| `Key` | The secret key that clients need, 43 characters long. `Show` displays it, `Copy` copies it, and `Regenerate` makes a new one. |
| `Copy for scripts` | Copies the two settings that `thronectl` reads, `THRONE_ADDR` and `THRONE_KEY`. With `Local network`, the address is the computer's address on your network. |
| `Status` | Whether Throne listens, for example `Listening on port 9095 (local network)`, or why not, for example `Port 9095 is in use`. |

- Changes take effect when you click `OK`. A new key, port or address closes the open connections. Scripts that use the old key stop working, so Throne asks before `Regenerate` replaces a saved key.
- With `Local network`, the window warns that devices on your network can control Throne with the key. Your firewall may ask whether to allow Throne.
- Throne writes every request that changes something to the log, with the address of the client, for example `[API] PUT /v1/connection from 127.0.0.1`. Lines that start with `[API]` also show refused connections.
- If the [kill switch](@/advanced/kill_switch.md) is on and the API listens on the local network, the kill switch lets the API's replies out, so clients can still connect.

## Install thronectl {#install-thronectl}

`thronectl` is a single program for Linux, Windows, macOS and FreeBSD. The `.deb` and `.rpm` packages and the Linux ZIP of Throne include it, and so does the Docker image of headless Throne. The Windows and macOS downloads of Throne do not.

- Download it from the [Throne-api releases](https://github.com/throneproj/Throne-api/releases), unpack it, and put `thronectl` (`thronectl.exe` on Windows) in a folder on your `PATH`.
- Or, with Go installed:

  ```bash
  go install github.com/throneproj/Throne-api/cmd/thronectl@latest
  ```

`thronectl version` prints its version.

## Connect thronectl to Throne {#connect}

`thronectl` reads the address of Throne and the key from environment variables:

| Variable | What it holds |
| --- | --- |
| `THRONE_ADDR` | The address and port, for example `127.0.0.1:9095`. Default: `127.0.0.1:9095`. |
| `THRONE_KEY` | The key itself. |
| `THRONE_KEY_FILE` | The path of a file that holds the key. |

`thronectl` never takes the key as a command-line argument, so it does not end up in your shell history. Keep it in a file that only you can read and point `THRONE_KEY_FILE` at it:

1. In Throne, click `Copy for scripts`.
2. Save the line `THRONE_KEY=…` without `THRONE_KEY=`, that is only the key, in a file, for example `~/.config/throne/api-key`. On Linux and macOS, run `chmod 600` on the file.
3. Set the variables, for example in `~/.bashrc`:

   ```bash
   export THRONE_ADDR=127.0.0.1:9095
   export THRONE_KEY_FILE=~/.config/throne/api-key
   ```

   In PowerShell, for example in `$PROFILE`:

   ```powershell
   $env:THRONE_ADDR = "127.0.0.1:9095"
   $env:THRONE_KEY_FILE = "$HOME\throne-api-key.txt"
   ```

4. Test the connection:

   ```bash
   thronectl status
   ```

These flags work before or after the command:

| Flag | What it does |
| --- | --- |
| `--addr host:port` | The address of Throne. Default: `THRONE_ADDR`, else `127.0.0.1:9095`. |
| `--key-file file` | Reads the key from this file. It wins over `THRONE_KEY` and `THRONE_KEY_FILE`. |
| `--json` | Prints the answer of Throne as JSON instead of a table. |
| `--timeout 30s` | Gives up after this time, `30s` by default. `0` waits as long as Throne needs. |

In a systemd unit, `thronectl` also takes the key from the credential `api.key` (`LoadCredential=api.key:/path/to/key`) when none of the above gives one, as `throne-web.service` for the [web UI](@/headless/web.md#systemd) does.

For headless Throne, `Throne --print-connection` prints the `export` lines for both variables. See [Connect thronectl to Throne](@/headless/thronectl.md#connect).

## Commands {#commands}

`thronectl help <command>` describes a command. `thronectl` shows profile, group, routing profile and test IDs in its lists; the commands take these IDs.

| Command | What it does |
| --- | --- |
| `status` | Shows the state, the running profile, the modes, the routing profile in use, the version of Throne and, if something else holds TUN, what it is. |
| `groups` | Lists the groups with their IDs. `ROUTE` shows the group's own routing profile, or `active` when the group follows the active one. |
| `profiles [--group N] [--name REGEX] [--ids 1,2]` | Lists profiles with their last test results. |
| `connect <id>` | Starts a profile. `--restart` restarts it if it is already running. |
| `connect last` | Starts the running profile, else the last one, else the remembered one. |
| `connect best [--group N] [--name REGEX] [--max-age 1h]` | Starts the profile with the lowest latency. It first tests the profiles that have no result newer than `--max-age`. The default group is the one that Throne shows. |
| `disconnect` | Stops the running profile. |
| `toggle` | `connect last` when nothing runs, else `disconnect`. |
| `tun on`, `tun off`, `tun toggle` | Turns TUN mode on or off. |
| `proxy on`, `proxy off`, `proxy toggle` | Turns the system proxy on or off. |
| `routes` | Lists the routing profiles. `*` marks the active one. A note marks the one that the running profile's group uses, and the ones that a subscription group updates. |
| `route <id>` | Makes a routing profile active and restarts the running profile, unless the running profile's group uses its own routing profile. |
| `test latency`, `test ip`, `test speed` | Runs a URL test, an IP test or a speed test, and shows the results. Add `--group N` (with an optional `--name REGEX`) or profile IDs. |
| `jobs`, `job <id>`, `cancel <id>` | Lists the recent tests, shows one test, stops a test. |
| `clear` | Clears test results. Add `--group N` (with an optional `--name REGEX`) or profile IDs. |
| `live` | Shows the current traffic rates. |
| `traffic [--by profile] [--by app] [--since 24h]` | Shows traffic totals by profile (default) or by app. `--since` also takes days, such as `7d`. |
| `logs [-n N] [-f]` | Prints the last `N` lines of Throne's log, 200 by default and 1000 at most. `-f` then prints new lines until you stop it, also when the connection drops or Throne restarts. With `--json`, one JSON object per line. |
| `import [--group N] FILE` | Imports share links, a subscription body, or a Clash, sing-box, Xray, WireGuard or OpenVPN configuration from a file of up to 900 KiB. `-` reads standard input; `--url URL` makes Throne download a URL once. The default group is the one that Throne shows. |
| `sub add <url> [--name NAME]` | Adds a subscription group, named after the host of the URL by default, and updates it. `--no-auto-update` leaves it out of automatic updates; `--no-update` does not download it now. `--route ID` gives the group its own routing profile; `--import-route` keeps the routing profile that the subscription provides. |
| `sub update <id>…`, `sub update --all` | Updates subscription groups, or all groups that are not archived. |
| `group add <name>`, `group rm <id>` | Adds a group; deletes a group with its profiles. |
| `profile rm <id>…`, `profile rm --group N [--name REGEX]` | Deletes profiles. |
| `settings [get] [KEY…]` | Shows the settings that the API manages. See [Settings](#settings). |
| `settings set KEY=VALUE…` | Changes settings. `true`, `false` and numbers are sent as such, anything else as text. |
| `backup create [-o FILE] [--parts LIST]` | Writes a `.thrbackup` archive, by default `throne-backup-<time>.thrbackup`. |
| `backup restore FILE [--parts LIST] [--yes]` | Shows what an archive holds, asks, restores it and restarts Throne. Default parts: `profiles,routes,otp,ip_lists`. |
| `rulesets update` | Downloads the remote rule-sets of the running profile again. |
| `geo`, `geo update [geoip] [geosite] [--only-missing]` | Shows the Xray geo files; downloads them again. |
| `restart` | Restarts Throne. |
| `shutdown [--yes]` | Quits Throne, after asking. |
| `web [--listen ADDR] …`, `web passwd` | Serves the [web UI](@/headless/web.md), which manages Throne from a browser, until you stop it; sets its password. |
| `keygen [-o FILE]` | Creates a new key: prints it, or writes it to a new file that only you can read. It does not connect to Throne. |
| `call <METHOD> <PATH> [key=value…] [--body JSON]` | Sends any request of the [API specification](https://github.com/throneproj/Throne-api/blob/main/spec/api.md) and prints the answer. |
| `completion bash`, `zsh`, `fish` or `powershell` | Prints a [shell completion](#completion) script. |
| `version` | Prints the version of `thronectl`. |

Good to know:

- `--name` is a regular expression that ignores case and may match any part of the name. Quote it in the shell, for example `--name 'DE|NL'`.
- `--parts` takes a comma-separated list of `profiles`, `routes`, `settings`, `otp`, `ip_lists` and `icons`.
- Commands that start or stop a profile wait for the result, up to `--timeout`. If Throne needs longer, `thronectl` shows the current state, such as `connecting`, and Throne finishes on its own. Imports, updates, deletions and downloads wait the same way. `--no-wait` returns at once.
- Tests and geo downloads can take minutes, so give them a longer `--timeout`, for example `--timeout 15m`. A test that takes longer keeps running in Throne; follow it with `thronectl job <id>`.
- Throne runs one test at a time, whether you start it from the window or through the API.
- A group can connect with its own routing profile (see [Routing profile per group](@/guides/routing.md#group-routing)). `route` then changes only the routing profile of the other groups. Through the API, you can give a group its own routing profile only when `sub add` creates it; to change it later, use the group options in Throne's window.
- `thronectl` exits with `0` on success; `1` for an error from Throne (printed as `error: <code>: <message>`), a failed subscription, rule-set or geo update, or a question answered with no; `2` for a wrong command or flag; `3` when it cannot connect, the key is rejected, or a file cannot be read.

### Settings {#settings}

`thronectl settings` shows the settings that the API can change, and `thronectl settings set` changes them, for example `thronectl settings set log_level=debug kill_switch=true`. Throne checks every value first and changes nothing if one is wrong.

| Area | Settings |
| --- | --- |
| Start | `remember_enable` |
| Logs | `log_level`, `xray_log_level` |
| Mixed inbound | `inbound_enabled`, `inbound_address`, `inbound_port` |
| Tests | `test_url`, `test_concurrency`, `test_timeout_ms` |
| Automatic updates | `sub_auto_update`, `sub_respect_server_interval`, `route_auto_update` (minutes, `0` is off) |
| Kill switch | `kill_switch` |
| TUN | `tun_strict_route`, `tun_auto_redirect` (Linux), `tun_mtu`, `tun_ipv6`, `tun_ipv4_cidr`, `tun_ipv6_cidr` |
| Ports and APIs | `dns_in_port`, `dns_in_lan`, `clash_api_port`, `clash_listen_addr`, `singbox_api_port` |
| DNS and time | `remote_dns`, `direct_dns`, `ntp_enabled`, `ntp_server`, `ntp_interval` |

`dns_in_lan` lets other devices on your network use the core's DNS server on `dns_in_port`; see [DNS server for other devices](@/guides/dns.md#dns-server).

Most changes to logs, the inbound, ports, DNS and NTP take effect when the running profile starts again. TUN settings need that restart only while the profile runs in TUN mode; otherwise they apply the next time TUN mode starts. While a restart is needed, `thronectl status` says so; `thronectl connect last --restart` applies them. The [settings table](https://github.com/throneproj/Throne-api/blob/main/spec/api.md#settings) of the specification has the allowed values.

## Admin requests {#admin-requests}

These commands send admin requests: `logs`, `import`, `sub add`, `group add`, `group rm`, `profile rm`, `settings set`, `backup`, `restart` and `shutdown`. `logs` (`GET /v1/logs`) is one because the log names the sites that connections went to. The other commands, including `sub update`, `rulesets update` and `geo update`, are control requests. The key is the same for both kinds.

- Throne answers admin requests only from its own computer, that is from `127.0.0.1` or `::1`.
- From another device, they fail with `forbidden`, unless `Allow management from the local network` is on. Headless Throne needs `--api-admin-lan` instead.
- Backup archives contain the passwords and keys of your profiles and your OTP secrets in plain text. `thronectl` creates them readable only by you. Keep them that way, and delete them when you no longer need them.
- `backup restore` refuses an archive whose profiles would run programs or read files on the computer, such as Extra Core profiles (`invalid_archive`). Restore such an archive in the Throne window.
- `backup restore` and `shutdown` ask before they act. In a script without a terminal, add `--yes`.

A restore of `settings` keeps the settings that belong to this computer: the Throne API, the inbound and ports, TUN, the kill switch, `Remember last profile`, NTP, logging, the system proxy and the paths.

## Examples {#examples}

Everyday commands:

```bash
thronectl status
thronectl profiles --group 3 --name 'DE|NL'
thronectl connect 12
thronectl tun on
thronectl route 2
thronectl --timeout 2m test latency --group 3
thronectl traffic --by app --since 7d
thronectl sub add 'https://sub.example.com/abc' --name Provider
thronectl --timeout 2m sub update --all
thronectl backup create -o throne.thrbackup
```

### Connect the best profile {#example-best}

Connect the profile with the lowest latency in the group that Throne shows. Results older than ten minutes are tested again:

```bash
thronectl connect best --max-age 10m
```

### Connect the fastest profile {#example-speed}

Run a speed test on the profiles of group 3 whose names contain `DE` or `NL`, then connect the fastest one. This needs [jq](https://jqlang.org/):

```bash
best=$(thronectl --json --timeout 15m test speed --group 3 --name 'DE|NL' |
  jq -r '[.results[] | select(.download_bps > 0)] | max_by(.download_bps) | .id // empty')
[ -n "$best" ] && thronectl connect "$best"
```

### One key to connect and disconnect {#example-hotkey}

To connect and disconnect with one key of a Stream Deck, a macro pad or a hotkey tool, let the key run:

```bash
thronectl toggle
```

Programs that start commands from a key often do not read your shell's settings. Set `THRONE_KEY_FILE` (and `THRONE_ADDR`, if needed) as an environment variable of your user account, or add `--key-file` to the command. Throne desktop also has [global hotkeys](@/reference/shortcuts.md#global-hotkeys) for `Start/Stop Profile` and `Toggle Tun Mode`.

### PowerShell {#example-powershell}

The speed test example in PowerShell:

```powershell
$job = thronectl --json --timeout 15m test speed --group 3 --name 'DE|NL' | ConvertFrom-Json
$best = $job.results | Where-Object download_bps -gt 0 | Sort-Object download_bps -Descending | Select-Object -First 1
if ($best) { thronectl connect $best.id }
```

## Shell completion {#completion}

`thronectl completion <shell>` prints a script that completes commands, flags, setting names, backup parts and other fixed values in bash, zsh, fish and PowerShell. When Throne answers within a second, it also completes the IDs of profiles, groups, routing profiles and tests, with their names.

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

The scripts ask `thronectl` what to complete, so they stay correct when you update `thronectl`.

## Security {#security}

- Whoever has the key controls the connection, the modes and the routing of Throne, and can read the names, addresses, test results and traffic of your profiles. From this computer, or from the local network when `Allow management from the local network` is on, the key also allows imports, deletions, settings changes and backups, which contain your credentials in plain text.
- Keep the key out of command lines and scripts that others can read. Use a key file that only you can read, or `THRONE_KEY` from a password manager or secret store.
- Every message between `thronectl` and Throne is encrypted and authenticated with the key, also on the local network. There is no forward secrecy: whoever learns the key can read traffic that was recorded earlier.
- If the key leaks, click `Regenerate` and give your scripts the new key. For headless Throne, see [Lockout Protection & Recovery](@/headless/recovery.md).
- Keep `Listen on` at `This computer only` unless you need the API on the network. On the network, list the devices that may connect in `Allowed addresses`.
- After 5 failed connection attempts within a minute from one address, Throne refuses that address for 60 seconds. This also locks out other clients at that address, for example behind the same router, until it passes. Throne never refuses `127.0.0.1`, or an address where a client logged in successfully in the last 10 minutes.
- Responses never contain the credentials of your profiles or the URLs of your subscriptions. Backups are the exception.

## Go library {#go-library}

The [Throne-api](https://github.com/throneproj/Throne-api) repository also contains a Go library, `github.com/throneproj/Throne-api`, with a method for every request, and the [API specification](https://github.com/throneproj/Throne-api/blob/main/spec/api.md) with the wire protocol, so you can write a client in another language.

A client that follows Throne does not need to poll. `GET /v1/events` waits until something changes and says what changed, such as the status, the groups, the profiles, the tests or the settings, so the client reads only that again. `GET /v1/logs` returns the log lines that came after the last ones the client has, and can also wait for them. `thronectl logs -f` and the [web UI](@/headless/web.md) use these two requests.

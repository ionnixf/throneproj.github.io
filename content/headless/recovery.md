+++
title = "Lockout Protection & Recovery"
description = "How headless Throne keeps its API within reach, and how to get back in when saved settings such as the kill switch or TUN lock you out."
weight = 50
toc = true
+++

## How Throne stays reachable {#protection}

- **The API is always on**, from the first start on `127.0.0.1:9095`. The `--api-*` [options](@/headless/command_line.md#options) override the saved API settings, and a settings restore keeps this device's API, inbound, TUN and kill switch settings.
- **The key file.** An empty `api.key` gets a new key. Any other key file that cannot be used keeps the API off: delete it, and the next start creates a new key.
- **The API's port.** When another program holds it, Throne runs without the API (`api_unavailable`) and tries again every 30 seconds. When another Throne holds it, give this one another port with `--api-listen PORT`.
- **Failed sign-ins** never lock out loopback, or an address whose client signed in during the last 10 minutes.
- **The kill switch** always lets loopback through, and the replies from the API's port on other addresses. In rootless Docker and other user-namespaced containers without `CAP_SETGID`, it fails closed (`unsupported`), and profiles are refused until you turn the `kill_switch` setting off.
- **TUN on Linux** can take the replies to remote API clients unless `tun_auto_redirect` is on, and strict route without `tun_ipv6` drops IPv6 clients. Throne warns once when TUN starts while the API listens on a network address.
- **A profile that crashes Throne** three starts in a row, within a minute of resuming it, is no longer resumed (`resume_suppressed`). Start it with `thronectl connect`.
- **A core that keeps failing** for three starts in a row no longer makes Throne exit with codes 70 or 71. Throne stays up with the API (`core_gave_up`) and tries the core again every 5 minutes.

## Recovering access {#recovery}

If saved settings still lock you out, such as a kill switch that ends your SSH session or TUN that takes the API's replies, start Throne once with `--no-resume --no-kill-switch` (or `THRONE_RESUME=0` and `THRONE_KILL_SWITCH=0`). No profile starts and the kill switch stays off, so `thronectl` on the same device can fix the setting, such as with `thronectl settings set kill_switch=false` or `thronectl tun off`. Then start Throne normally again.

**systemd:**

```bash
printf 'THRONE_RESUME=0\nTHRONE_KILL_SWITCH=0\n' | sudo tee -a /etc/throne/throne.env >/dev/null
sudo systemctl restart throne
thronectl settings set kill_switch=false        # or: thronectl tun off
# remove the two lines from /etc/throne/throne.env again, then:
sudo systemctl restart throne
```

**Docker:** add `THRONE_RESUME: "0"` and `THRONE_KILL_SWITCH: "0"` under `environment:` in `compose.yaml` and run `docker compose up -d`. Fix the setting with `docker compose exec throne thronectl …`, remove the variables and run `docker compose up -d` again.

**macOS:** add the two variables to the plist's `EnvironmentVariables` and reload it. Fix the setting, remove them and reload again:

```bash
sudo launchctl bootout system/moe.Throne.headless
sudo launchctl bootstrap system /Library/LaunchDaemons/moe.Throne.headless.plist
```

**Windows service:** stop it with `sc.exe stop Throne`, then start Throne by hand from an elevated prompt:

```bat
start "" /wait "C:\Program Files\Throne\Throne.exe" --headless --data-dir C:\ProgramData\Throne --no-resume --no-kill-switch
```

Fix the setting with `thronectl` from another elevated prompt (see [Commands from a prompt](@/headless/installation.md#windows-prompt)), stop Throne with `Ctrl+C` and run `sc.exe start Throne`.

**Starting over:** stop Throne and move its data directory aside (for the system unit, `/var/lib/throne`). The next start creates an empty one with a new API key. Restore your profiles from a [backup](@/headless/thronectl.md#from-desktop).

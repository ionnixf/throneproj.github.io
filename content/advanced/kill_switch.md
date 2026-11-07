+++
title = "Kill Switch"
description = "Block all traffic that does not go through Throne, also while no profile runs, and get your connection back if the kill switch locks you out."
weight = 65
toc = true
+++

While the kill switch is on, your computer can send traffic only through Throne: through TUN mode, the local proxy port, or Throne's own connections. Everything else is blocked, also while no profile runs, so apps cannot fall back to your normal connection when the proxy stops or drops.

{% alert_warning() %}
Read [If it locks you out](#recovery) before you turn on the kill switch.
{% end %}

## What it does {#what-it-does}

| Situation | What gets through |
| --- | --- |
| A profile runs in TUN mode | Everything, through the TUN adapter. |
| A profile runs with the system proxy only | Apps that use the proxy. Apps that ignore it are blocked. |
| No profile runs | Only the local network and the [exceptions below](#allowed). |

Use TUN mode with the kill switch, so that all apps keep working while a profile runs. The kill switch ends when Throne exits or crashes: a crash does not cut you off, but it does not protect you after that either.

## Turn it on {#enable}

1. Open `Settings` → `Basic Settings` → `Security` (in Simple mode, the `Security` section of the `Common` tab).
2. Tick `Kill Switch` and click `OK`.

It needs the same rights as TUN mode, see [Requirements](#requirements). On Linux and macOS, turning it on restarts Throne's core and a running profile. It starts again with Throne until you turn it off.

The window title then shows `[Kill switch]`, and the Start button has a lock:

- **Green:** a profile runs in TUN mode.
- **Amber:** no profile runs or TUN mode is off, so most traffic is blocked.
- **Red:** the kill switch has a problem, see [When it cannot work](#failures).

To turn it off, untick `Kill Switch` again, or right-click the Start button → `Disable Kill Switch`.

## What is always allowed {#allowed}

- **Throne's core:** connections to your servers, traffic that your rules send `direct`, URL tests and Throne's own DNS servers. [Extra Core](@/advanced/chains.md#extra-core) programs too.
- **The TUN adapter and loopback** (`127.0.0.1`, `::1`), which apps use to reach the local proxy port.
- **The local network:** `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`, `169.254.0.0/16`, `224.0.0.0/4`, `255.255.255.255`, `fc00::/7`, `fe80::/10` and `ff00::/8`. Turning off `Private Range Bypass` in `Settings` → `Tun Settings` blocks them too; editing its list does not change these ranges.
- **Network basics:** DHCP, IPv6 neighbor and router discovery, and multicast group messages, so your computer keeps its address.
- **Replies of the [Throne API](@/advanced/api.md)** when it listens on a network address.

DNS queries (ports 53 and 853) from other programs to local addresses are still blocked, because your router would send them outside the tunnel. On Linux, Throne warns when `/etc/resolv.conf` names such a server: in TUN mode, use systemd-resolved, which Throne points at the tunnel, or a DNS server outside your network.

Everything else is blocked, including:

- Throne's own requests, such as subscription updates, update checks and WARP registration. They work only in TUN mode, or while a profile runs with `System Proxy` or `Settings` → `Basic Settings` → `Miscellaneous` → `Use proxy` on.
- Traffic that this computer forwards for other devices, containers or virtual machines, unless it goes through the TUN adapter or stays in the local network.
- Other VPN apps.

## Requirements {#requirements}

| System | What the kill switch needs |
| --- | --- |
| Windows | Throne runs as administrator, and the `Base Filtering Engine` service runs. |
| Linux | The core runs as root through the SUID bit; capabilities (`setcap`) are not enough. Linux 5.12 or newer with nftables and conntrack labels (`CONFIG_NFT_CT`, `CONFIG_NF_CONNTRACK_LABELS`). |
| macOS | The core runs as root through the SUID bit. `/etc/pf.conf` loads the `com.apple` anchors, as it does by default. |

Throne sets up these rights as for TUN mode, see [TUN mode privileges](@/guides/tun_mode.md#privileges).

## When it cannot work {#failures}

When the kill switch is on but cannot start or stops working, Throne shows "Kill switch is not active" with the reason. Click `Restart as Administrator` (Windows) or `Grant Privileges` (Linux, macOS) when rights are missing, `Retry` for other problems, or `Disable Kill Switch`. Until then nothing is blocked, and Throne refuses to start profiles.

The log and `kill_switch_error` in the [Throne API](@/advanced/api.md) give the reason as a code:

| Code | Meaning |
| --- | --- |
| `privilege` | Rights are missing, see [Requirements](#requirements). |
| `unsupported` | The system lacks something else from [Requirements](#requirements), such as nftables or a root core on Linux. |
| `config` | Invalid kill switch settings, such as the TUN address. |
| `tampered` | Another program keeps removing the rules or turning the firewall off. |
| `crash`, `timeout`, `internal` | The guard process stopped or did not start in time. `Retry` often helps. |
| `overridden` | Headless Throne was started with `--no-kill-switch`. |

On Linux in a container or user namespace without `CAP_SETGID`, the kill switch keeps blocking everything and Throne refuses profiles (`[Kill switch is blocking traffic]` in the title) until you click `Disable Kill Switch`.

## If it locks you out {#recovery}

Throne's settings work without internet access, so turn the kill switch off there or from the Start button.

If Throne does not respond:

1. Quit Throne, or end it in the task manager. Its guard process removes the firewall rules within a few seconds.
2. If traffic is still blocked, end the guard process yourself:
   - **Windows:** in Task Manager → `Details`, end every `ThroneCore.exe`, or restart Windows.
   - **Linux:** run `sudo pkill -f 'ThroneCore --guard'`.
   - **macOS:** run `sudo pkill -f 'ThroneCore --guard'`. If the rules stay, start Throne again (its core removes them) or restart macOS.
3. Start Throne and turn the kill switch off before you do anything else.

## On Android {#android}

Throne for Android has no kill switch. Use Android's own: set Throne as the always-on VPN and turn on "Block connections without VPN". See [Always-on VPN](@/android/permissions.md#always-on).

## Headless Throne {#headless}

[Headless Throne](@/headless/_index.md) has the same kill switch: `thronectl settings set kill_switch=true`. On Linux, it needs a unit that runs as root or a core with the SUID bit, see [Privileges on Linux](@/headless/installation.md#linux-privileges). If it cuts you off from the computer, start Throne once with `--no-kill-switch`, see [Recovering access](@/headless/recovery.md#recovery).

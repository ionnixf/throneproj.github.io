+++
title = "TUN Mode"
description = "Turn on TUN mode, give Throne the rights it needs on Windows, Linux and macOS, adjust the TUN settings, and fix common TUN problems."
weight = 30
toc = true
+++

In TUN mode, Throne creates a virtual network adapter and sends the traffic of the whole computer through it, also DNS queries and apps that ignore proxy settings. To choose between TUN mode and the system proxy, see [Which mode to use](@/guides/proxy_modes.md#which-mode).

## Turn on TUN mode {#enable}

1. Tick `Tun Mode` on the main window, or use `Operation Mode` → `Tun Mode` in the tray menu.
2. The first time, Throne asks for the rights that TUN mode needs. Accept the request, see [Privileges](#privileges).
3. Start a profile, if none is running.

Turning TUN mode on or off restarts the running profile. TUN mode works only while a profile runs; to block traffic while none runs, turn on the [kill switch](@/advanced/kill_switch.md).

## Privileges {#privileges}

TUN mode needs system rights to create the adapter and change the routes of the computer. If you give the rights yourself, for example with the Linux capabilities below, `Settings` → `Basic Settings` → `Security` → `Disable Privilege request` stops Throne from asking (Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)).

### Windows {#privileges-windows}

- Throne must run as administrator. The first time you tick `Tun Mode`, click `Yes` and accept the Windows prompt: Throne restarts as administrator, with TUN mode on. If Windows Firewall then asks about `ThroneCore`, allow it.
- From then on, Throne asks for administrator rights each time it starts. To stop this, tick `Settings` → `Basic Settings` → `Security` → `Always Start as Standard User` (Advanced mode only); Throne then asks when you turn on TUN mode.
- `Program` → `Start with system`, turned on while Throne runs as administrator, starts Throne as administrator at sign-in, without a prompt.

See also [FAQ](@/help/faq.md#tun-admin).

### Linux {#privileges-linux}

- Only the core, `ThroneCore`, needs root rights. The first time you tick `Tun Mode`, click `Yes` and enter your password: Throne uses `pkexec` (part of polkit) to make root the owner of `ThroneCore` and set its SUID bit. Then tick `Tun Mode` again.
- Never start Throne itself with `sudo`.
- Instead of the SUID bit, you can give the core these five capabilities. Throne accepts the core only with all five:

```bash
sudo setcap cap_net_admin,cap_net_raw,cap_net_bind_service,cap_sys_ptrace,cap_dac_read_search+ep /path/to/Throne/ThroneCore
```

Some third-party packages cannot change the core themselves and show the package's own instructions instead. See also [FAQ](@/help/faq.md#linux-suid).

### macOS {#privileges-macos}

- Move `Throne.app` to `/Applications` before you turn on TUN mode. Setting the rights can fail while the app is in `Downloads`.
- The first time you tick `Tun Mode`, click `Yes`. Terminal opens with a `sudo` command for the core. Enter your password there, then tick `Tun Mode` again.
- `sudo` needs an administrator account. On a standard account, use the system proxy instead.

## TUN settings {#settings}

Open `Settings` → `Tun Settings`. If TUN mode is on, turn it off and on again to apply changes. In Simple mode, only `Tun Enable IPv6`, `Strict Route`, `Enable Tun Routing` and `Restart Core` are shown (see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)).

| Setting | Default | What it does |
| --- | --- | --- |
| `MTU` | `1500` | The largest packet size of the adapter, from 1000 to 10000. With `Tun Enable IPv6`, use at least 1280, or IPv6 may fail. |
| `Tun Enable IPv6` | off | Sends IPv6 traffic through the tunnel. Turn it on only if your server supports IPv6. |
| `Strict Route` | on for Windows 10 and later, off elsewhere | On Windows, blocks DNS queries that go around the tunnel; see [Windows DNS Leak Protection](@/advanced/windows_tun_mode.md#strict-route). |
| `Enable Tun Routing` | off | Keeps IP ranges and `geoip-*` rule-sets that your routing sends `direct` out of the tunnel. Large rule-sets can cause very high CPU use on Windows. |
| `Auto Redirect` | on | Linux only. Improves routing and speed with nftables. While it is on, this computer cannot be a network gateway for other devices. |
| `L3 Bridge Bypass` | off | Sends direct UDP and ICMP traffic straight out of the network adapter: faster, and keeps NAT working for games and P2P. Not on Windows on ARM. |
| `IPv4 CIDR`, `IPv6 CIDR` | `172.19.0.1/24`, `fdfe:dcba:9876::1/96` | The addresses of the adapter. Change them only if they clash with a network you use. |
| `Private Range Bypass` | on | Address ranges that go straight to your network adapter instead of through the tunnel, one per line. |
| `Restart Core` | – | Stops the running profile and restarts the core, for when TUN mode does not start. |

The default `Private Range Bypass` list is `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`, `169.254.0.0/16`, `224.0.0.0/4`, `fc00::/7`, `fe80::/10` and `ff00::/8`.

## Side effects {#side-effects}

### Local network and virtual machines {#side-effects-vms}

With `Private Range Bypass` on, printers, network drives, your router and other local devices are reached directly, outside the tunnel, unless a routing rule sends their range to the proxy or blocks it. Docker, WSL and virtual machines bypass the tunnel the same way; if their network uses a range that is not in the list, add it. See [Recipes](@/guides/recipes.md#docker-wsl-vms).

### Mobile hotspot on Windows {#side-effects-hotspot}

If Windows Mobile Hotspot or Internet Connection Sharing shares your normal network adapter, TUN mode fails, and Throne warns "IPv4 forwarding breaks Tun mode". In the Mobile hotspot settings, set "Share my internet connection from" to `throne-tun`, or turn the hotspot off while you use TUN mode. See [Recipes](@/guides/recipes.md#hotspot).

### Other VPNs {#side-effects-vpn}

Two VPNs on one computer compete for routes and DNS. Turn off other VPN apps while TUN mode is on. To reach a company network at the same time, see [Recipes](@/guides/recipes.md#corporate-vpn).

### DNS {#side-effects-dns}

All DNS queries reach Throne, but a leak test can still show your internet provider for sites that go direct; see [DNS](@/guides/dns.md#leak-tests).

On macOS, while TUN mode runs, Throne sets the DNS server of your network services, such as `Wi-Fi`, to `127.0.0.53`, where the core answers with Throne's DNS settings. When TUN mode stops, it puts your previous servers back.

## Troubleshooting {#troubleshooting}

Open the `Logs` tab and look for errors when TUN mode starts. For general steps, see [Troubleshooting](@/help/troubleshooting.md#tun).

### Windows {#troubleshooting-windows}

**Connected, but nothing loads.** Windows Firewall may block `ThroneCore`. Allow it in `Windows Security` → `Firewall & network protection` → `Allow an app through firewall`, for private and public networks.

**Antivirus.** Some antivirus programs, such as Avast and ESET, block the adapter or the core. Add an exception for the Throne folder.

**"Strict routing unavailable".** Untick `Strict Route` in `Tun Settings` and start the profile again. Without it, DNS queries can leak; see [Windows DNS Leak Protection](@/advanced/windows_tun_mode.md).

**"Tun device misbehaving".** Click `Reset` to restart the core, then start the profile again.

**"Leftover Tun adapter".** After a crash or force-quit, Windows can keep the adapter of the old session. Click `Start Again`. If it keeps failing, restart Windows.

**TUN mode takes very long to start.** Throne clears the Windows DNS cache when TUN mode starts, which is very slow with a huge HOSTS file, such as an ad-blocking list. Make it smaller.

### Linux {#troubleshooting-linux}

**"Please install "pkexec" first."** Install polkit with your package manager, or set the capabilities shown in [Linux privileges](#privileges-linux).

**"file exists" when TUN mode starts.** Turn off `Auto Redirect` in `Tun Settings`.

**No DNS with systemd-resolved.** If `DNSOverTLS` is on for all interfaces in `/etc/systemd/resolved.conf`, TUN mode breaks. Turn it off there, or for the Throne adapter while it exists:

```bash
sudo resolvectl dnsovertls throne-tun no
```

**DNS stays broken after TUN mode stops, with a local DNS proxy.** A local DNS proxy on `127.0.0.1`, such as `dnscrypt-proxy`, `unbound` or `dnsmasq`, can have its connection to an upstream server by IP caught in the tunnel; restarting the proxy brings DNS back. To prevent this, add the upstream address to `Private Range Bypass` in `Tun Settings`, for example `1.1.1.1/32` for `https://1.1.1.1/dns-query` (Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)). Then turn TUN mode off and on, and restart the proxy.

**Packages from AUR, Nix or other repositories.** They are maintained by the community. If TUN mode fails there, try the official build; see [FAQ](@/help/faq.md#third-party-packages).

### macOS {#troubleshooting-macos}

**An error about "the system DNS" when TUN mode starts.** The core could not point the system DNS to itself (see [DNS](#side-effects-dns)); the rest of the message says why. Click `Restart Core` in `Tun Settings`, then start the profile again.

**No DNS after Throne was force-quit or crashed.** If a network service still uses the DNS server `127.0.0.53`, start Throne once: its core puts back your previous servers. Or remove `127.0.0.53` from that service in the macOS network settings.

## On Android {#android}

In `VPN` mode, Throne for Android works like TUN mode. Its settings are in `Settings` → `TUN / VPN`; see [VPN & Proxy Modes](@/android/modes.md#tun-settings).

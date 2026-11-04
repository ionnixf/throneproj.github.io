+++
title = "TUN Mode"
description = "Turn on TUN mode, give Throne the rights it needs on Windows, Linux and macOS, adjust the TUN settings, and fix common TUN problems."
weight = 30
toc = true
+++

In TUN mode, Throne creates a virtual network adapter and sends the traffic of the whole computer through it. This covers apps that ignore proxy settings, and DNS queries too. Use it when the system proxy is not enough. For a comparison of both modes, see [Which mode to use](@/guides/proxy_modes.md#which-mode).

## Turn on TUN mode {#enable}

1. Tick `Tun Mode` on the main window, or use `Operation Mode` → `Tun Mode` in the tray menu.
2. The first time, Throne asks for the rights that TUN needs. Accept the request (see below).
3. Start a profile, if none is running.

While TUN mode is active, the window title shows `[Tun]`. It starts with `[Admin]` when Throne has the rights for TUN. Turning TUN mode on or off restarts the running profile.

TUN mode works only while a profile runs. When you stop the profile, your apps use your normal connection again. To block their traffic while no profile runs, turn on the [kill switch](@/advanced/kill_switch.md) (since 1.4.0). With `Program` → `Remember last profile` on, Throne starts the last profile again when it starts, and turns TUN mode back on if it was on when you quit.

What happens the first time:

- **Windows:** Throne asks "Please run Throne as admin". Click `Yes`. Throne closes and starts again as administrator, with TUN mode on; Windows shows a user account control prompt first. Windows Firewall may then ask about `ThroneCore`. Allow it: if you block it, TUN connects, but nothing loads.
- **Linux:** Throne asks "Please give the core root privileges". Click `Yes` and enter your password in the system prompt. Throne then gives the core root rights. Tick `Tun Mode` again.
- **macOS:** Throne asks "Please give the core root privileges". Click `Yes`. Terminal opens and runs a `sudo` command. Enter your password in Terminal, then tick `Tun Mode` again.

## Privileges {#privileges}

TUN mode needs system rights to create the adapter and change the routes of the computer.

### Windows {#privileges-windows}

- Throne must run as administrator. The first click on `Tun Mode` offers a restart as administrator.
- After Throne has run as administrator once, it asks for administrator rights each time it starts. To stop this, tick `Settings` → `Basic Settings` → `Security` → `Always Start as Standard User` (Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)). Throne then asks again each time you turn on TUN mode.
- If you turn on `Program` → `Start with system` while Throne runs as administrator, Throne starts with administrator rights at sign-in, without a prompt.

See also [FAQ](@/help/faq.md#tun-admin).

### Linux {#privileges-linux}

- Only the core, `ThroneCore`, needs root rights. Throne gives it these rights with `pkexec`: it makes root the owner of `ThroneCore` and sets the SUID bit (`chown root:root` and `chmod u+s`).
- `pkexec` is part of polkit. If it is missing, Throne shows "Please install "pkexec" first."
- Never start Throne itself with `sudo`.
- Instead of the SUID bit, you can give the core these five capabilities: `CAP_NET_ADMIN`, `CAP_NET_RAW`, `CAP_NET_BIND_SERVICE`, `CAP_SYS_PTRACE` and `CAP_DAC_READ_SEARCH`. Throne accepts the core as privileged only if it has all five.

```bash
sudo setcap cap_net_admin,cap_net_raw,cap_net_bind_service,cap_sys_ptrace,cap_dac_read_search+ep /path/to/Throne/ThroneCore
```

Some third-party packages cannot change the core themselves. They show "This installation cannot grant the core privileges by itself." together with instructions from the package. See also [FAQ](@/help/faq.md#linux-suid).

### macOS {#privileges-macos}

- Move `Throne.app` to `/Applications` before you turn on TUN mode. The rights are set from Terminal, and this can fail while the app is in `Downloads`.
- Throne opens Terminal with a `sudo chown root:wheel … && sudo chmod u+s …` command for the core. Enter your password there, then tick `Tun Mode` again.
- `sudo` needs an administrator account. On a standard account you can use the system proxy, but not TUN mode.

### Turn off the requests {#disable-privilege-request}

`Settings` → `Basic Settings` → `Security` → `Disable Privilege request` (Advanced mode only) stops Throne from asking for rights. Throne then starts TUN mode with the rights it has, and the log says "User opted for no privilege req, some features may not work". Use this only if you give the rights yourself, for example when you always start Throne as administrator, or when you set the capabilities shown above.

## TUN settings {#settings}

Open `Settings` → `Tun Settings`. If TUN mode is on when you click `OK`, Throne shows "Restart Tun to take effect". Turn `Tun Mode` off and on again.

In Simple mode, the window shows only `Tun Enable IPv6`, `Strict Route`, `Enable Tun Routing` and `Restart Core`. The other settings are hidden, but keep their values; see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode).

| Setting | Default | What it does |
| --- | --- | --- |
| `MTU` | `1500` | The largest packet size of the adapter. Choose `1500` or `9000`, or type a value from 1000 to 10000. Since 1.4.0, `OK` does not accept other values, and Throne warns when `Tun Enable IPv6` is on and the value is below 1280, because the system may then drop IPv6. |
| `Tun Enable IPv6` | off | Gives the adapter an IPv6 address, so that IPv6 traffic goes through the tunnel. |
| `Strict Route` | on for Windows 10 and later, off elsewhere | On Windows, blocks DNS queries that try to go around the tunnel. See [Windows DNS Leak Protection](@/advanced/windows_tun_mode.md#strict-route). |
| `Enable Tun Routing` | off | Leaves the IP ranges and `geoip-*` rule-sets that your routing sends `direct` out of the tunnel, so the system routes them past the core. Large rule-sets can cause very high CPU use on Windows. |
| `Auto Redirect` | on | Linux only. Improves TUN routing and speed with nftables. While it is on, this computer cannot be used as a network gateway for other devices. |
| `L3 Bridge Bypass` | off | Sends UDP and ICMP traffic that your rules send `direct` straight out of the network adapter, instead of opening a new connection for it. This is faster and keeps NAT working for games and P2P. TCP keeps the normal direct path. Not available on Windows on ARM. |
| `IPv4 CIDR` | `172.19.0.1/24` | The IPv4 address of the adapter. Change it only if it clashes with a network you use. `Restore default addresses` resets both addresses. |
| `IPv6 CIDR` | `fdfe:dcba:9876::1/96` | The IPv6 address of the adapter. Used only with `Tun Enable IPv6`. |
| `Private Range Bypass` | on | Address ranges that go straight to your network adapter instead of through the core, one per line. `Restore default ranges` restores the list. Loopback and broadcast addresses always bypass the tunnel. |
| `Restart Core` | – | Stops the running profile and restarts the core. Use it when TUN mode does not start, then start the profile again. |

The default `Private Range Bypass` list is `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`, `169.254.0.0/16`, `224.0.0.0/4`, `fc00::/7`, `fe80::/10` and `ff00::/8`.

Since 1.4.0, `Tun Settings` has no `Stack` setting. Throne always uses the network stack that is built into the core, and ignores a stack that you chose in an older version.

## Side effects {#side-effects}

### Local network {#side-effects-lan}

With `Private Range Bypass` on, your computer reaches printers, network drives, your router's settings page and other local devices directly, outside the tunnel. If you turn it off, local traffic goes through the core, and your routing rules decide where it goes. A routing rule that sends a private range to the proxy, or blocks it, keeps that range in the tunnel so that the rule works.

### Mobile hotspot and connection sharing on Windows {#side-effects-hotspot}

If Windows Mobile Hotspot or Internet Connection Sharing shares your normal network adapter, Windows ignores the settings that keep Throne's own connections out of the tunnel. TUN mode then fails, and Throne warns "IPv4 forwarding breaks Tun mode". Share the hotspot from the `throne-tun` adapter instead: in the Windows settings for Mobile hotspot, set "Share my internet connection from" to `throne-tun`. Or turn the hotspot off while you use TUN mode. See [Recipes](@/guides/recipes.md#hotspot).

### Docker, WSL and virtual machines {#side-effects-vms}

Containers and virtual machines have their own virtual networks. Traffic between them and your computer uses private addresses, so it usually bypasses the tunnel. If such a network uses a range that is not in the `Private Range Bypass` list, add it. On Linux, remember that `Auto Redirect` stops the computer from working as a network gateway. See [Recipes](@/guides/recipes.md#docker-wsl-vms).

### Other VPNs {#side-effects-vpn}

Two VPNs on the same computer compete for routes and DNS. Turn off other VPN apps while TUN mode is on. To reach a company network at the same time, see [Recipes](@/guides/recipes.md#corporate-vpn).

### IPv6 {#side-effects-ipv6}

`Tun Enable IPv6` is off by default. Turn it on only if your server supports IPv6. Otherwise, you can get connection problems.

### DNS {#side-effects-dns}

In TUN mode, the DNS queries of all apps reach Throne. A DNS leak test can still show the DNS server of your Internet provider for sites that your rules send direct. This is expected; see [DNS](@/guides/dns.md#leak-tests).

**macOS:** since 1.4.0, while TUN mode runs, the core adds the TUN adapter to the network configuration of macOS as a network service of its own. Its DNS server is an address inside the tunnel: the address after the `IPv4 CIDR` address, `172.19.0.2` by default. macOS sends its DNS queries there, and the core answers them with Throne's DNS settings.

- Throne does not change the DNS servers of your network services, for example `Wi-Fi`, and the macOS network settings do not show the TUN service.
- When TUN mode stops, or when the core exits or crashes, macOS removes the TUN service again.
- If the core cannot add the service, TUN mode does not start; see [macOS troubleshooting](#troubleshooting-macos).

Before 1.4.0, Throne set the DNS server to the address of the TUN adapter and cleared the DNS servers when TUN mode stopped, so DNS servers that you had set yourself were lost. After a crash, macOS kept a DNS server that no longer answered.

## Troubleshooting {#troubleshooting}

Open the `Logs` tab and look for errors when TUN mode starts. For general steps, see [Troubleshooting](@/help/troubleshooting.md#tun).

### Windows {#troubleshooting-windows}

**Connected, but nothing loads.** Windows Firewall may block `ThroneCore`. Windows asks only once, when you first turn on TUN mode. If you blocked it then, allow `ThroneCore` in `Windows Security` → `Firewall & network protection` → `Allow an app through firewall`, for private and public networks.

**Antivirus.** Some antivirus programs, for example Avast and ESET, block the adapter or the core. Add an exception for the Throne folder.

**"Strict routing unavailable".** Windows could not turn on strict routing. Untick `Strict Route` in `Tun Settings` and start the profile again. Without it, DNS queries can leak; see [Windows DNS Leak Protection](@/advanced/windows_tun_mode.md).

**"Tun device misbehaving".** Click `Reset` to restart the core, then start the profile again.

**After a crash or force-quit.** Windows can keep the virtual adapter of the old session. Since 1.4.0, Throne then shows "Leftover Tun adapter" when TUN mode starts. Click `Start Again`: this usually works. If it keeps failing, restart Windows. In this case, starting can take about 15 seconds before the message appears. Wait for it instead of restarting Throne, which can leave another adapter behind.

**TUN mode takes very long to start.** Throne clears the DNS cache of Windows when TUN mode starts. A very large HOSTS file, for example an ad-blocking list with 100,000 entries, makes this very slow. Make the HOSTS file smaller.

**Mobile hotspot.** See [Mobile hotspot and connection sharing](#side-effects-hotspot).

### Linux {#troubleshooting-linux}

**"Please install "pkexec" first."** Install polkit with your package manager, or set the capabilities shown in [Linux privileges](#privileges-linux).

**"file exists" when TUN mode starts.** This error came from `Auto Redirect` and was fixed in 1.2.2. If you still see it, turn off `Auto Redirect`.

**No DNS with systemd-resolved.** If `DNSOverTLS` is turned on for all interfaces in `/etc/systemd/resolved.conf`, TUN mode breaks. Turn it off there, or turn it off for the Throne adapter while the adapter exists:

```bash
sudo resolvectl dnsovertls throne-tun no
```

**DNS stops working after TUN mode stops, with a local DNS proxy.** If a local proxy such as `dnscrypt-proxy`, `unbound` or `dnsmasq` serves DNS on `127.0.0.1`, its connection to an upstream server by IP can be caught in the tunnel. DNS can then stay broken after TUN mode stops. Flushing the DNS cache does not help, and only a restart of the network or of the proxy recovers DNS.

To prevent it, add the upstream address to `Settings` → `Tun Settings` → `Private Range Bypass`, one address range per line. For `https://1.1.1.1/dns-query`, add `1.1.1.1/32`. The proxy then reaches that server through your network adapter, outside the tunnel. Turn TUN mode off and on again after you change the list, then restart the proxy so that it opens a new connection. In Simple mode this setting is hidden; see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode).

**Packages from AUR, Nix or other repositories.** They are maintained by the community. If TUN mode fails there, try the official build; see [FAQ](@/help/faq.md#third-party-packages).

**Throne was started with `sudo`.** Quit it and start it as your normal user. Only the core needs root.

### macOS {#troubleshooting-macos}

**macOS says that the app is damaged or cannot be opened.** Remove the quarantine flag:

```bash
xattr -d com.apple.quarantine /Applications/Throne.app
```

**The rights request fails.** Move `Throne.app` to `/Applications`, start it from there, and tick `Tun Mode` again.

**Standard account.** TUN mode needs an administrator account. Use the system proxy, or sign in with an administrator account.

**"set the system DNS" when TUN mode starts.** Since 1.4.0, TUN mode does not start when the core cannot add the TUN service and its DNS server to macOS (see [DNS](#side-effects-dns)). The rest of the message says what failed. Click `Restart Core` in `Tun Settings`, then start the profile again.

If a network service in the macOS network settings still has the DNS server `127.0.0.53`, an earlier test version of 1.4.0 left it there. Start Throne once: when its core has root rights, it puts back the DNS servers that the service had before. Or remove `127.0.0.53` from the DNS servers of that network service yourself.

For DNS leak hardening on Windows, see [Windows DNS Leak Protection](@/advanced/windows_tun_mode.md).

## On Android {#android}

In `VPN` mode, Throne for Android works like TUN mode. Its settings are in `Settings` → `TUN / VPN`. There, the default MTU is 9000, and strict route is always on. Since 2.0.2, the stack cannot be chosen there either. See [VPN & Proxy Modes](@/android/modes.md#tun-settings).

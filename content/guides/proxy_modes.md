+++
title = "System Proxy, TUN & LAN Sharing"
description = "Choose between the system proxy and TUN mode, share Throne with other devices on your network, and set up the local proxy port."
weight = 20
toc = true
+++

The system proxy and TUN mode work only while a profile runs. Throne then also listens on the mixed port, `127.0.0.1:2080` by default, for SOCKS and HTTP proxy connections.

When no profile runs, or when an app goes around the system proxy, its traffic uses your normal connection. To block it instead, turn on the [kill switch](@/advanced/kill_switch.md).

## System proxy {#system-proxy}

Tick `System Proxy` on the main window, or use `Operation Mode` → `System Proxy` in the tray menu. While a profile runs, Throne points your system's proxy setting to the mixed port. No administrator rights are needed.

Only apps that follow the system proxy setting use it, such as most browsers. Others connect directly, for example Discord, Telegram calls, terminals, Microsoft Store (UWP) apps and many games.

- **Windows:** Throne writes the Windows proxy setting in the `Proxy Format` of the [inbound settings](#inbound-settings).
- **Linux:** only GNOME and KDE are supported. Run Throne as your normal user, not as root, or it cannot change the setting.
- **macOS:** Throne sets the HTTP, HTTPS and SOCKS proxy of every enabled network service.

Many command-line tools ignore the system proxy but read `HTTP_PROXY` and `HTTPS_PROXY`:

```bash
export HTTP_PROXY=http://127.0.0.1:2080 HTTPS_PROXY=http://127.0.0.1:2080
```

```powershell
$env:HTTP_PROXY = "http://127.0.0.1:2080"; $env:HTTPS_PROXY = "http://127.0.0.1:2080"
```

- If Throne is killed or crashes while a profile runs, the setting stays and your apps cannot connect. See [FAQ](@/help/faq.md#force-quit).
- While `System Proxy` is ticked, Throne's own requests, such as subscription updates, also go through the mixed port.

## TUN mode {#tun-mode}

Tick `Tun Mode` on the main window, or use `Operation Mode` → `Tun Mode` in the tray menu. Throne then sends the traffic of the whole computer, DNS included, through a virtual network adapter. This needs administrator or root rights; see [TUN Mode](@/guides/tun_mode.md).

## Which mode to use {#which-mode}

| | System proxy | TUN mode |
| --- | --- | --- |
| Apps covered | Only apps that follow the system proxy | All apps |
| DNS | Apps can still use your normal DNS server | Handled by Throne's [DNS settings](@/guides/dns.md#how-dns-works) |
| Rules for apps (`processName:`, `processPath:`) | Reach only apps that use the proxy | Work for every app |
| Typical problems | Apps that ignore the proxy | Firewalls, antivirus or other VPNs that block the adapter |

The system proxy is enough for a browser. Use TUN mode for apps that ignore it, for calls and games, and for rules for single apps (see [Recipes](@/guides/recipes.md#calls-and-games)). You can also turn on both: apps that follow the system proxy then use the mixed port, and TUN mode catches the rest.

With `Program` → `Remember last profile` on, Throne starts the last profile when it starts, and turns `System Proxy` and `Tun Mode` back on if they were on when you quit.

## Share the proxy with other devices {#lan-sharing}

Other devices on your network, such as phones, TVs or game consoles, can use Throne on your computer as their proxy.

1. Choose `Program` → `Allow other devices to connect`, and restart the running profile.
2. Read your computer's address and port in the status bar, for example `Mixed: 192.168.1.20:2080`.
3. Allow incoming connections to the port in your firewall: on Windows, allow `ThroneCore` when Windows Firewall asks (or later in `Allow an app through firewall`); on Linux, for example `sudo ufw allow 2080`; on macOS, allow it when macOS asks.
4. On the other device, set a SOCKS5 or HTTP proxy with that address and port.

{% alert_warning() %}
Without a password, anyone on your network can use your proxy. Turn on `Enable Authorization` in the [inbound settings](#inbound-settings), set `Inbound Username` and `Inbound Password`, and enter them on the other devices.
{% end %}

Other devices, such as a router, can also use Throne's [DNS server](@/guides/dns.md#dns-server). To share your whole connection as a Wi-Fi hotspot instead, see [Recipes](@/guides/recipes.md#hotspot).

## Inbound settings {#inbound-settings}

These settings control the mixed port: `Settings` → `Basic Settings` → `Common` → `Inbound Settings`. In Simple mode only `Listen Port` is shown (see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)). Restart a running profile to apply changes.

| Setting | Default | What it does |
| --- | --- | --- |
| `Listen Address` | `127.0.0.1` | `127.0.0.1` accepts only apps on this computer; `::` or `0.0.0.0` also accepts other devices. `Allow other devices to connect` sets `::`. |
| `Listen Port` | `2080` | The mixed port, for SOCKS (4, 4a and 5) and HTTP. |
| `Random port` | off | Picks a free port at each start, shown in the status bar. |
| `Enable Authorization` | off | Requires `Inbound Username` and `Inbound Password`. |
| `Disable Mixed Inbound` | off | Turns the mixed port off. `System Proxy` then cannot be turned on, and `Use proxy` for Throne's own requests works only in TUN mode. |
| `Custom Inbound` | empty | Extra sing-box inbounds, as a JSON object with an `inbounds` list. |
| `Proxy Format` | `{ip}:{port}` | Windows only. `{ip}:{port}` or `http://{ip}:{port}` announces an HTTP proxy, `socks={ip}:{port}` a SOCKS proxy. |

## On Android {#android}

Throne for Android has a `VPN` mode, which works like TUN mode and is the default, and a `Proxy only` mode. See [VPN & Proxy Modes](@/android/modes.md).

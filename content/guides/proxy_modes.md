+++
title = "System Proxy, TUN & LAN Sharing"
description = "Choose between the system proxy and TUN mode, share Throne with other devices on your network, and set up the local proxy port."
weight = 20
toc = true
+++

Throne can take the traffic of your apps in two ways: as the system proxy, or in TUN mode. This page explains both, helps you choose, and shows how other devices on your network can use Throne too.

Both modes work only while a profile runs. Throne then also listens on a local proxy port, the mixed port. By default it is `127.0.0.1:2080`, and it accepts both SOCKS and HTTP proxy connections.

When no profile runs, or when an app goes around the system proxy, its traffic uses your normal connection. To block such traffic instead, turn on the [kill switch](@/advanced/kill_switch.md) (since 1.4.0).

## System proxy {#system-proxy}

Tick `System Proxy` on the main window, or use `Operation Mode` → `System Proxy` in the tray menu. While a profile runs, Throne sets the proxy setting of your operating system to the mixed port. When you stop the profile or quit Throne, it removes the setting again.

- Only apps that follow the system proxy setting use it. Most browsers do.
- Apps that ignore the setting connect directly, as if Throne were not running. Examples: Discord, Telegram calls, terminals and command-line tools, Microsoft Store (UWP) apps and many games. Use [TUN mode](#tun-mode) for them.
- No administrator rights are needed.

How Throne sets the proxy depends on the system:

- **Windows:** Throne writes the Windows proxy setting. `Proxy Format` (see [Inbound settings](#inbound-settings)) decides the text it writes: `{ip}:{port}` (the default) and `http://{ip}:{port}` announce an HTTP proxy, `socks={ip}:{port}` a SOCKS proxy.
- **Linux:** only GNOME and KDE are supported. On other desktops, set the proxy in each app. Run Throne as your normal user, not as root, or it cannot change your desktop's proxy setting.
- **macOS:** Throne sets the HTTP, HTTPS and SOCKS proxy of every enabled network service.

Many command-line tools ignore the system proxy but read the `HTTP_PROXY` and `HTTPS_PROXY` environment variables:

```bash
export HTTP_PROXY=http://127.0.0.1:2080 HTTPS_PROXY=http://127.0.0.1:2080
```

```powershell
$env:HTTP_PROXY = "http://127.0.0.1:2080"; $env:HTTPS_PROXY = "http://127.0.0.1:2080"
```

More details:

- `System Proxy` cannot be turned on while `Disable Mixed Inbound` is on. Throne then shows "Cannot set system proxy when mixed inbound is disabled."
- `Settings` → `Basic Settings` → `Miscellaneous` → `Restart Proxy On System Proxy Disable` (Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)) restarts the running profile when you turn the system proxy off, so that open connections close.
- While `System Proxy` is ticked, Throne also sends its own requests, such as subscription updates, through the mixed port.
- If Throne is killed or crashes while a profile runs, it cannot remove the setting, and your apps cannot connect. See [FAQ](@/help/faq.md#force-quit).
- To switch the system proxy with a key, set the global hotkey `Toggle System Proxy`. See [Keyboard Shortcuts & Tray](@/reference/shortcuts.md#global-hotkeys).

## TUN mode {#tun-mode}

Tick `Tun Mode` on the main window, or use `Operation Mode` → `Tun Mode` in the tray menu. Throne then creates a virtual network adapter and sends the traffic of the whole computer through it while a profile runs.

- Every app is covered, including apps that ignore proxy settings.
- DNS queries go through the adapter too, and Throne answers them with its own [DNS settings](@/guides/dns.md#how-dns-works).
- TUN mode needs administrator rights on Windows and root rights for the core on Linux and macOS. Throne asks for them the first time.

For setup, settings and troubleshooting, see [TUN Mode](@/guides/tun_mode.md).

## Which mode to use {#which-mode}

| | System proxy | TUN mode |
| --- | --- | --- |
| Apps covered | Only apps that follow the system proxy setting | All apps |
| Rights needed | None | Administrator (Windows), root for the core (Linux, macOS) |
| DNS | Not captured. Apps can still look up names with your normal DNS server. | Captured and handled by Throne's DNS settings |
| Rules for apps (`processName:`, `processPath:`) | Reach only apps that use the proxy | Work for every app |
| Traffic that passes through Throne | Only the traffic of apps that use the proxy | All traffic, also traffic that your rules send direct, except bypassed address ranges |
| Typical problems | Apps that ignore the proxy | Firewalls, antivirus programs or other VPNs that block the adapter |

In short: the system proxy is enough for a browser. Use TUN mode for apps that ignore the system proxy and for calls and games. Rules for single apps also work reliably only in TUN mode, because apps that ignore the system proxy never reach Throne. See also the [Recipes](@/guides/recipes.md#calls-and-games).

You can turn on both modes at the same time. Apps that follow the system proxy then use the mixed port, and TUN mode catches all other traffic. The window title then shows `[Tun+System Proxy]`.

With `Program` → `Remember last profile` on, Throne starts the last profile when it starts, and turns `System Proxy` and `Tun Mode` back on if they were on when you quit.

Since 1.4.0, the `Hijack` tab of `Routing Settings` and the Windows `System DNS` option are gone. They served apps that ignore the system proxy; use `Tun Mode` for them instead.

## Share the proxy with other devices {#lan-sharing}

Other devices on your network, such as phones, TVs, game consoles or other computers, can use Throne on your computer as their proxy.

1. Choose `Program` → `Allow other devices to connect`. Throne now listens on all network interfaces instead of only on `127.0.0.1`.
2. If a profile is running, click `Restart` in the "Settings changed, restart to apply" notice, or stop and start the profile.
3. Find your computer's address in the status bar. It now shows `Mixed:` followed by your LAN address and the port, for example `Mixed: 192.168.1.20:2080`.
4. Allow incoming connections to the port in your firewall (see below).
5. On the other device, set the proxy server to that address and port. Use the type SOCKS5 or HTTP.

{% alert_warning() %}
Without a password, anyone on your network can use your proxy. Turn on `Enable Authorization` in the [inbound settings](#inbound-settings) and set `Inbound Username` and `Inbound Password`. Then enter the same username and password on the other devices.
{% end %}

Firewall:

- **Windows:** when the core first accepts connections from the network, Windows Firewall asks whether to allow `ThroneCore`. Allow it. If you blocked it earlier, allow `ThroneCore` in `Windows Security` → `Firewall & network protection` → `Allow an app through firewall`.
- **Linux:** if a firewall is active, open the port, for example with `sudo ufw allow 2080`.
- **macOS:** if the macOS firewall is on, allow incoming connections when macOS asks.

While Throne listens on all interfaces, the `Connections` tab shows a `Source` column with the address of each device. To stop sharing, choose `Program` → `Allow other devices to connect` again and restart the profile. Throne then listens on `127.0.0.1` only.

`Allow other devices to connect` shares only the proxy port. Since 1.4.0, other devices can also use Throne's DNS server, for example a router; see [DNS server for other devices](@/guides/dns.md#dns-server).

Sharing the proxy port is different from sharing your whole connection as a Wi-Fi hotspot. For that and more examples, see [Recipes](@/guides/recipes.md#share-with-devices).

## Inbound settings {#inbound-settings}

These settings control the mixed port. Open `Settings` → `Basic Settings` → `Common` and look under `Inbound Settings`. In Simple mode, the group shows only `Listen Port`; the other settings are hidden, but keep their values. To set a username and password for [LAN sharing](#lan-sharing), switch to Advanced mode (see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)).

| Setting | Default | What it does |
| --- | --- | --- |
| `Listen Address` | `127.0.0.1` | The address that the mixed port listens on. `127.0.0.1` accepts only apps on this computer. `::` or `0.0.0.0` also accepts other devices; `Allow other devices to connect` sets `::`. |
| `Listen Port` | `2080` | The mixed port. It accepts SOCKS (4, 4a and 5) and HTTP proxy connections. |
| `Random port` | off | Picks a free port each time Throne starts. The status bar shows the current port. |
| `Enable Authorization` | off | Requires the `Inbound Username` and `Inbound Password` for every connection to the mixed port. |
| `Disable Mixed Inbound` | off | Turns the mixed port off. `System Proxy` then cannot work. `Use proxy` works only while TUN mode is on (since 1.4.0; before, not at all). TUN mode still works. |
| `Custom Inbound` | empty | `Edit` opens a JSON editor for extra sing-box inbounds: an object with an `inbounds` list. |
| `Proxy Format` | `{ip}:{port}` | Windows only. The text that Throne writes to the Windows proxy setting. |

If a profile is running when you change these settings, restart it to apply them.

## On Android {#android}

Throne for Android has two service modes instead: `VPN`, which works like TUN mode and is the default, and `Proxy only`, which only opens a local proxy port. It also has per-app proxy and LAN access. See [VPN & Proxy Modes](@/android/modes.md).

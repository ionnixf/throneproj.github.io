+++
title = "Quick Start"
description = "Add your servers, test them, choose a routing profile and connect with Throne on Windows, Linux or macOS."
weight = 2
toc = true
+++

You need a subscription link or a share link from your provider, or the details of your own server. Every step here works in Simple mode, which a new installation starts in. For Android, see [Throne for Android](@/android/_index.md#quick-start).

## Add servers {#add-servers}

Throne stores each server as a **profile**. Profiles belong to **groups**, the tabs above the profile list. A **subscription** is a group that Throne downloads from a URL and can update later.

### Add a subscription {#paste-subscription}

1. Copy the subscription URL. It starts with `https://`.
2. In the main window, press `Ctrl+V` (or `Program` → `Add profile from clipboard`).
3. Choose `Create new subscription group` and click `OK`.

Throne creates a group named after the host in the URL and downloads its profiles. If your provider sends plan details, a card above the profile list shows your used traffic and expiry date. `Add profiles to this group` would import the profiles only once, without the URL, so they cannot be updated.

Update the current group later with `Groups` → `Update subscription` (`Ctrl+U`). For automatic updates, see [Subscriptions & Groups](@/guides/subscriptions.md#auto-update).

### Add single profiles {#single-profiles}

Profiles go into the group whose tab is selected.

- **Share links** such as `vless://`, `ss://` or `trojan://`: copy one or more, one per line, and press `Ctrl+V`.
- **A QR code on your screen**: `Program` → `Scan QR Code` (`Ctrl+Shift+Q`).
- **Files and QR code images**: drag them onto the main window, or `Program` → `Add profile from File(s)` (`Ctrl+O`).
- **By hand**: `Program` → `New profile` (`Ctrl+N`).

All supported links and files are listed in [Protocols & Import Formats](@/reference/protocols.md#import-formats).

## Test servers {#test-servers}

Click `Groups` → `Url Test Group` (`Ctrl+Shift+G`) to measure the delay of every profile in the group. To test only some, select them, right-click and choose `Url Test Selected` (`Ctrl+Shift+S`).

The `Test Result` column shows the delay in milliseconds, or `Unavailable`. Click its header to sort. `Groups` → `Clean up` → `Remove Unavailable` (`Ctrl+Shift+R`) deletes the failed profiles; a subscription update adds them again if the provider still lists them.

For speed tests and automatic server choice, see [Testing & Auto Selector](@/guides/testing.md).

## Choose routing {#choose-routing}

This step is optional: the built-in `Default` routing profile sends all traffic through the proxy. To open the websites of your own country directly:

1. Click `Routing` → `Download Profiles` and choose `China`, `Iran` or `Russia`. Keep `Auto update` ticked and click `OK`.
2. Open the `Routing` menu again and choose `Bypass China`, `Bypass Iran` or `Bypass Russia` at the bottom. A running profile restarts with the new routing.

A "Bypass" profile proxies everything except the websites and IP addresses of that country and your local network. For the other downloaded profiles and your own rules, see [Routing](@/guides/routing.md#download-profiles).

## Connect {#connect}

1. Click the profile that you want to use.
2. Press `Enter`, or click the Start button to the right of `Tools`.
3. Tick `System Proxy` or `Tun Mode`, next to the Start button.

| Mode | What it does |
|---|---|
| `System Proxy` | Points the proxy settings of your system to Throne (`127.0.0.1:2080` by default). Browsers and other apps that follow these settings use the proxy. |
| `Tun Mode` | Captures the traffic of all apps, including apps that ignore proxy settings. Needs administrator or root rights. |

You can turn on both. With neither, apps that you set up yourself can still use Throne as a SOCKS5 or HTTP proxy at `127.0.0.1:2080`. See [Which mode to use](@/guides/proxy_modes.md#which-mode).

The first time you tick `Tun Mode`, Throne asks for the rights it needs. Click `Yes`, then:

- **Windows:** confirm the Windows prompt. Throne restarts as administrator with `Tun Mode` on. If Windows Firewall asks about `ThroneCore`, allow it.
- **Linux:** enter your password, then tick `Tun Mode` again.
- **macOS:** enter your password in the Terminal window that opens, then tick `Tun Mode` again.

See [TUN privileges](@/guides/tun_mode.md#privileges) for details.

### Check the connection {#check-connection}

The bottom left corner of the window shows the running profile and, a moment later, the country and city of your exit IP address. Click it to test the connection: `Test Result: <n> ms` means it works. If you see `Unavailable`, try another profile or see [Troubleshooting](@/help/troubleshooting.md).

### Stop and quit {#stop-and-quit}

- To disconnect, click the Start button again (now `Stop`), or press `Ctrl+S`.
- Closing the window only hides Throne in the tray. To quit, use `Program` → `Exit` or `Exit` in the tray menu.
- `Program` → `Remember last profile` reconnects when Throne starts, and `Program` → `Start with system` starts Throne when you log in.

{% alert_warning() %}
Always quit Throne with `Exit`. If Throne is killed while `System Proxy` is on, your system keeps using a proxy that no longer runs, and websites stop loading. See the [FAQ](@/help/faq.md#force-quit).
{% end %}

## Simple and Advanced mode {#interface-mode}

**Simple** mode shows the options most people need and hides advanced menu items, settings and tabs, such as `Preset Settings`, the `Logging` and `Core` tabs of `Basic Settings`, most DNS options and the advanced options of the profile editor. **Advanced** mode shows everything. A new installation starts in Simple mode; an updated one keeps Advanced mode.

Hidden settings keep their values and still apply, so switching the mode changes nothing else. To switch, open `Settings` → `Basic Settings` → `Style`, set `Interface` to `Simple` or `Advanced` and click `OK`.

To find any setting, press `Ctrl+K` and type part of its name. In Simple mode, this also finds hidden settings; choosing one switches to Advanced mode and opens it. See [Command palette](@/reference/shortcuts.md#command-palette).

In Simple mode, some tabs that these docs name are sections of another tab. For example, the `Subscription` and `Security` tabs of `Basic Settings`, and the `Warp` tab of `Routing Settings`, are sections of their `Common` tab.

## Next steps {#next-steps}

- [Subscriptions & Groups](@/guides/subscriptions.md): automatic updates, and the User-Agent and HWID settings that some providers need.
- [Routing](@/guides/routing.md): your own rules, for example to send only a few websites through the proxy.
- [Testing & Auto Selector](@/guides/testing.md): speed tests, and automatic switching to a working server.
- [TUN Mode](@/guides/tun_mode.md) and [DNS](@/guides/dns.md): settings, side effects and leak tests.
- [Troubleshooting](@/help/troubleshooting.md) and [FAQ](@/help/faq.md): help when something does not work.

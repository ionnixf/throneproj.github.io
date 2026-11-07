+++
title = "Windows DNS Leak Protection"
description = "How Strict Route stops DNS leaks in TUN mode on Windows, and what to do when you cannot use it."
weight = 60
toc = true
aliases = ["/advanced/windows-tun-mode/"]
+++

In [TUN mode](@/guides/tun_mode.md), Windows can still send DNS queries through your normal network adapter, so your internet provider sees which sites you look up. (In `System Proxy` mode, `Strict Route` has no effect.)

## Strict Route {#strict-route}

Windows sends each DNS query to all network adapters at once, so it also leaves outside the tunnel. While TUN mode runs, `Strict Route` adds Windows firewall filters that:

- block DNS (port 53) on every adapter except the tunnel;
- block other apps' IPv6 connections while `Tun Enable IPv6` is off.

The filters can also block programs that need DNS or IPv6 outside the tunnel.

`Strict Route` is on by default on Windows 10 and 11. To check, open `Settings` → `Tun Settings`, make sure `Strict Route` is ticked, click `OK`, and turn `Tun Mode` off and on again.

### When it is off {#when-off}

- **Windows 7, 8 or 8.1:** `Strict Route` does not work there.
- **"Strict routing unavailable":** Windows could not set up the filters, so the profile did not start. Turning `Strict Route` off, as the message suggests, removes the protection. Turn it back on once you have fixed the cause.
- **First installed with Throne 1.2.0:** that version had it off, and updates keep saved settings. Tick it.

## Registry fallback {#registry-fallback}

Without `Strict Route`, turn off the Windows feature that sends DNS queries to all adapters (Group Policy `Turn off smart multi-homed name resolution`). This is weaker: it changes which DNS servers Windows asks but blocks nothing. It needs Windows 8 or later.

### With Group Policy (Pro, Enterprise, Education) {#group-policy}

1. Open `Edit group policy` from the Start menu.
2. Go to `Computer Configuration` → `Administrative Templates` → `Network` → `DNS Client`.
3. Double-click `Turn off smart multi-homed name resolution`, select `Enabled` and click `OK`.
4. Restart Windows.

{% alert_warning() %}
Select `Enabled`, not `Disabled`: enabling the policy turns the feature off.
{% end %}

To undo it, set the policy back to `Not Configured`.

### With the registry (Windows Home) {#windows-home}

Right-click `Command Prompt` in the Start menu, choose `Run as administrator`, run this command and restart Windows:

```text
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\DNSClient" /v DisableSmartNameResolution /t REG_DWORD /d 1 /f
```

To undo it, run this the same way and restart:

```text
reg delete "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\DNSClient" /v DisableSmartNameResolution /f
```

## Browsers {#browsers}

### Encrypted DNS {#encrypted-dns}

DNS over HTTPS in a browser, or for a network adapter on Windows 11, does not use port 53. `Strict Route` does not block it and it skips Throne's DNS, so a leak test shows that DNS provider. Turn off secure DNS in your browser's privacy or security settings, and DNS over HTTPS for the adapter in Windows Settings.

### QUIC {#quic}

QUIC does not cause DNS leaks, but if Google, YouTube and other QUIC (HTTP/3 over UDP) sites are slow or fail while other sites work, your proxy may carry UDP badly. In Chrome, open `chrome://flags/`, set `Experimental QUIC protocol` to `Disabled` and click `Relaunch`.

## Still leaking? {#still-leaking}

- **Check your routing.** Domains that your rules send direct are looked up with `Direct DNS` on purpose. See [DNS leak tests](@/guides/dns.md#leak-tests) for the expected results.
- **Pause security software.** Some antivirus and firewall products filter or redirect DNS. Test without them, or add an exception for Throne.
- **Disconnect other VPN apps.** They change DNS settings and firewall rules too.
- **Use the [kill switch](@/advanced/kill_switch.md).** It blocks all traffic that bypasses Throne, including DNS to your local network, even while no profile runs.

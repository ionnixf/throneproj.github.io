+++
title = "Deep Links"
description = "Use throne:// links to add subscriptions, routing profiles and proxy profiles with one click, on desktop and Android."
weight = 70
toc = true
+++

A deep link is a `throne://` URL that tells Throne to add a subscription, a routing profile, remote routing profiles or one proxy profile. Providers put them on websites and in QR codes.

## How Throne receives a deep link {#receive}

Clicking a link works only when `throne://` is registered for Throne. The Windows installer, the Linux `.deb` and `.rpm` packages and install script, and `Throne.app` on macOS register it for your user account. A ZIP copy on Windows or Linux does not: open `Settings` → `Basic Settings` → `Common`, tick `Register throne:// links at startup` (Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)) and click `OK`. With several copies, links open in the last one that started with this option on; `Install` next to it registers the current copy.

Without registration, copy the link and press `Ctrl+V` in the main window, or drag the link text onto it. Throne also takes a link as a [command-line argument](@/reference/files.md#command-line).

{% alert_info() %}
On desktop, `Scan QR Code` and dropped QR images import only `add` links. For another deep link in a QR code, copy the text after `QR Code Result:` from the `Logs` tab and press `Ctrl+V`.
{% end %}

[Headless Throne](@/headless/_index.md) does not handle `throne://` links. Use `thronectl sub add` for subscriptions and `thronectl import` for `add` links, see [Throne API & thronectl](@/advanced/api.md#commands).

## Link format {#format}

```text
throne://<command>/<payload>
```

- Write `throne://` in lowercase. The command is not case-sensitive.
- The slash after the command is required.
- Throne ignores anything after `?` or `#` in the link, so everything goes into the payload.
- A percent-encoded payload (`%2B` instead of `+`) is decoded first.

| Command | What it does | Asks first? |
| --- | --- | --- |
| `addsub` | Adds a subscription group and updates it | Yes |
| `route` | Imports a routing profile carried inside the link | Yes |
| `remoteroute` | Adds remote routing profiles by URL | Yes |
| `add` | Adds one proxy profile to the current group | No. On Android, yes when tapped in another app. |

### Base64 rules {#base64}

| Command | Accepted payload |
| --- | --- |
| `addsub`, `remoteroute` | Standard Base64 only (`+` and `/`), with or without `=` padding. URL-safe Base64 with `-` or `_` fails. |
| `route`, `add` | URL-safe (`-` and `_`) or standard Base64, with or without padding. Throne writes URL-safe Base64 without padding. |

Keep the payload on one line, without spaces. To encode on Linux or in Git Bash (on macOS, leave out `-w 0`):

```bash
printf '%s' 'https://example.com/sub/abc123#MyProvider' | base64 -w 0
```

In PowerShell:

```powershell
[Convert]::ToBase64String([Text.Encoding]::UTF8.GetBytes('https://example.com/sub/abc123#MyProvider'))
```

Both print standard Base64 with padding.

## `addsub`: add a subscription {#addsub}

```text
throne://addsub/<base64>
```

The payload is the Base64 of `<subscription_url>#<group_name>`. Leave the URL as it is; it needs no percent-encoding. The `#<group_name>` part is optional (default: the host of the URL), and a plain space in it works. For example, `https://example.com/sub/abc123#MyProvider` gives:

```text
throne://addsub/aHR0cHM6Ly9leGFtcGxlLmNvbS9zdWIvYWJjMTIzI015UHJvdmlkZXI=
```

Throne asks first, with a ticked `Auto update` checkbox, then creates the group and updates it.

{% alert_info() %}
`Auto update` only lets the group take part in automatic updates. These also need `Basic Settings` → `Subscription` → `Subscription auto update` → `Enable`, which is off by default. See [Subscriptions](@/guides/subscriptions.md#auto-update).
{% end %}

## `route`: import a routing profile {#route}

```text
throne://route/<base64>
```

Carries a whole routing profile. To create one, select the profile on the `Route` tab of `Routing Settings` and click `Export` (`Ctrl+C`). The imported profile does not become active until you choose it in the `Routing` menu.

The payload is the Base64 of a JSON object:

```json
{
  "kind": "throne-route-profile",
  "v": 1,
  "name": "Example",
  "default_outbound": "proxy",
  "rules": []
}
```

```text
throne://route/eyJraW5kIjoidGhyb25lLXJvdXRlLXByb2ZpbGUiLCJ2IjoxLCJuYW1lIjoiRXhhbXBsZSIsImRlZmF1bHRfb3V0Ym91bmQiOiJwcm94eSIsInJ1bGVzIjpbXX0
```

- `default_outbound` is `proxy`, `direct`, `block` or `warp-bypass`.
- A raw profile carries `"raw": true` and a `route` object instead of `rules`.
- An optional `dns` object carries the profile's own DNS settings: `remote_dns`, `remote_dns_disable_ipv6`, `direct_dns`, `direct_dns_disable_ipv6`, `dns_final_out`, `enable_dns_routing`, `fake_dns` and `fakeip_disable_ipv6` (see [DNS per routing profile](@/guides/dns.md#route-dns)). A rule can carry `"disabled": true`.
- Rules name the saved servers they send traffic to. Throne reports names it cannot find.

`Import` (`Ctrl+V`) on the `Route` tab also accepts the Base64 payload alone or the plain JSON. See [Share profiles](@/guides/routing.md#share-profiles).

## `remoteroute`: add remote routing profiles {#remoteroute}

```text
throne://remoteroute/<base64>
```

Carries only URLs: Throne downloads each routing profile and can keep it up to date. Use it to hand out a routing profile that you maintain. The payload is the Base64 of a plain list, not JSON, with one URL per line:

```text
<profile_url_1>#<name_1>
<profile_url_2>#<name_2>
```

- Lines that do not start with `http://` or `https://` are skipped. `#<name>` is optional (default: the host of the URL).
- Each URL must serve a structured routing profile: a `throne://route/` link, its Base64 payload or its JSON. Raw profiles cannot be remote.

For example, this command:

```bash
printf '%s\n%s' 'https://example.com/routes/bypass-iran.json#BypassIran' 'https://example.com/routes/ads.json' | base64 -w 0
```

gives this link:

```text
throne://remoteroute/aHR0cHM6Ly9leGFtcGxlLmNvbS9yb3V0ZXMvYnlwYXNzLWlyYW4uanNvbiNCeXBhc3NJcmFuCmh0dHBzOi8vZXhhbXBsZS5jb20vcm91dGVzL2Fkcy5qc29u
```

Throne asks first, with one ticked `Auto update` checkbox for all the profiles, then downloads them.

- An update replaces the rules and the default outbound of a profile, not its name.
- Scheduled updates also need `Basic Settings` → `Subscription` → `Routing profiles auto update` → `Enable` (Advanced mode only), which is off by default. See [Remote profiles](@/guides/routing.md#remote-profiles).
- URLs on `raw.githubusercontent.com` are downloaded through the `Remote Rule-set Mirror`, which may serve an older copy for a while after you change the file.

## `add`: add one proxy profile {#add}

```text
throne://add/<base64>
```

The payload is the Base64 of the profile's outbound settings as JSON. On desktop, the profile is added to the current group at once, like a pasted `vless://` link. `add` links also work wherever share links work: in subscriptions, files and QR codes.

To create one, right-click profiles → `Share` → `Copy links of selected (Deep Links)` (`Ctrl+Alt+C`, Advanced mode only), or use `Share` → `QR Code and link` with `Deep Link` ticked. A deep link carries the full configuration, including advanced connection options such as a bind interface. `Copy links of selected` (`Ctrl+C`) gives a deep link only for profiles that have no standard share link.

## On Android {#android}

Throne for Android handles the same four commands and creates `add` and `route` links. It also accepts `clash://install-config?url=…&name=…` links, which add a subscription like `addsub`; percent-encode the `url` value. The `throne://` scheme is registered when you install the app.

Tap the link in another app, or use `Add profile` → `Import from clipboard` or `Scan QR code` on the `Profiles` screen. QR codes work for all four commands, and `route` and `remoteroute` links also work with `Import` on the `Routing` screen. The differences:

- An `add` link tapped in another app asks first. From the clipboard or a QR code, it is imported without asking.
- Raw routing profiles (`"raw": true`) are refused, and endpoints in a shared routing profile are dropped.
- Scheduled updates need `Settings` → `Subscriptions` → `Subscription auto update` and, for remote routing profiles, `Settings` → `Routing` → `Auto update remote profiles`. Both are off by default.

## Troubleshooting {#troubleshooting}

These messages are from Throne desktop.

- **Clicking a link does nothing, or the browser asks which app to use.** `throne://` is not registered for this copy, see [How Throne receives a deep link](#receive). With several copies, click `Install` in the one that should open links. Or copy the link and press `Ctrl+V` in Throne.
- **Linux: links still do not open.** Install `desktop-file-utils`, then click `Install` next to `Register throne:// links at startup`.
- **Nothing happens and the log stays empty.** Throne silently drops an `addsub` payload that is not valid standard Base64 (URL-safe `-` or `_`, spaces or line breaks), a link without the slash after the command (except `add` and `remoteroute`), and an unknown command whose payload does not decode.
- **"Ignored deeplink with unknown command: …"** The word after `throne://` is not `addsub`, `route`, `remoteroute` or `add`.
- **"The link did not contain a subscription URL."** The `addsub` payload has no URL before the `#`.
- **"Deep link has no data" or "Base64 is invalid."** Nothing follows `throne://remoteroute/`, or the payload is not standard Base64.
- **"The link did not contain any valid http(s) routing profile URLs."** No line of the `remoteroute` payload starts with `http://` or `https://`. Send a list of URLs, not JSON.
- **"The link did not contain any valid remote routing profiles."** The link has no slash after `remoteroute`.
- **"The link could not be parsed:"** (`route` links). "Empty input": nothing follows `throne://route/`. "Input is not valid JSON, base64, or a Throne route link": the payload is damaged or cut off. "Unrecognized route object": the JSON has no `"kind": "throne-route-profile"`.
- **"Imported 0 profile(s)" in the log.** An `add` payload is damaged, or you scanned a QR code with another kind of deep link.
- **"Remote routing profile … failed: …" in the log.** The profile was added, but its URL cannot be downloaded or does not serve a structured routing profile. Fix it, then click `Update` on the `Route` tab.

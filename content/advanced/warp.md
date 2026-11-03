+++
title = "Cloudflare WARP"
description = "Add Cloudflare WARP as an extra exit behind your proxy, generate its config, turn it on, and let chosen traffic skip it."
weight = 40
toc = true
+++

WARP is Cloudflare's free VPN service. Throne can add WARP as an extra hop after your proxy. Websites then see a Cloudflare address instead of the address of your proxy server. This helps with sites that block VPN or data-center addresses.

## How WARP works in Throne {#how-it-works}

With WARP on, traffic goes: your device → the profile you start → WARP → Internet.

- WARP applies to every profile you start, including chains and auto selectors. Only `Custom (sing-box config)` profiles run without it. If the group has a landing proxy, WARP comes after it.
- WARP runs through your proxy, so the proxy must carry WARP's traffic:
  - `WireGuard` and `AmneziaWG` modes use UDP. The profile must forward UDP.
  - `MASQUE` mode over HTTP/3 also needs UDP. MASQUE over HTTP/2 needs only TCP.
- Rules and the default outbound can send traffic through your proxy without WARP. See [Skip WARP for some traffic](#warp-bypass).

Setting up WARP takes two steps: generate a config once, then turn WARP on.

## Generate a WARP config {#generate}

1. Open `Routing` → `Routing Settings`.
2. Open the `Warp` tab.
3. Choose the `Mode`: `WireGuard`, `MASQUE` or `AmneziaWG`.
4. Click `Generate Warp Config`.
5. The first time, Throne asks whether you accept the Cloudflare WARP terms of service. Click `Yes`.
6. Wait until the button shows "Success!". The fields of the chosen mode are now filled in.
7. Click `OK`.

In Simple mode, `Warp` is a section of the `Common` tab that shows only `Enable Warp`, `Mode` and `Generate Warp Config`. The fields below and `Registration Domains…` are hidden (Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)).

`WireGuard` and `MASQUE` have their own fields. If you switch between them later, generate a config for the other mode too. `AmneziaWG` uses the fields of `WireGuard`; see [AmneziaWG mode](#amneziawg).

| Mode | Fields |
| --- | --- |
| `WireGuard`, `AmneziaWG` | `Endpoint`, `Private Key`, `Public Key`, `Interface Addresses`, `Reserved` |
| `MASQUE` | `Endpoint`, `Private Key`, `Peer Public Key`, `Interface Addresses`, `SNI`, `HTTP Version` |

The MASQUE `HTTP Version` can be `HTTP/3 (fallback to HTTP/2)` (default), `HTTP/3 only` or `HTTP/2`. HTTP/3 runs over UDP. HTTP/2 runs over TLS on TCP and works where UDP is blocked.

Each generation registers a new device with Cloudflare.

### AmneziaWG mode {#amneziawg}

Since 1.4.0, `AmneziaWG` mode makes WARP's WireGuard connection harder to recognize. Before the WireGuard handshake, it sends extra packets: a few that look like the start of an HTTP/3 (QUIC) connection to `www.cloudflare.com`, and four random junk packets of 40 to 70 bytes. These packets are compatible with WARP, so the connection works as in `WireGuard` mode. Use this mode when `WireGuard` mode does not connect because WireGuard to Cloudflare is blocked. WARP's packets leave from your proxy server, so what counts is the network of the server, not yours.

The QUIC packets are called the signature. Throne creates them offline, on your computer, and stores them with the settings:

- when you choose `AmneziaWG` in `Mode` and no signature exists yet;
- each time you click `Generate Warp Config` in this mode, together with a new WireGuard config.

A WireGuard config that you generated earlier keeps working, so you do not need to generate again when you switch from `WireGuard` to `AmneziaWG`. If the signature is missing while `Enable Warp` is ticked, `Routing Settings` shows "AmneziaWG has no QUIC signature yet: click Generate Warp Config.", or the error that stopped its creation.

### If registration fails {#registration-fails}

Cloudflare's registration server, `api.cloudflareclient.com`, is blocked in some countries ([#1874](https://github.com/throneproj/Throne/issues/1874)). In that case, register through your proxy:

1. Start a profile that works.
2. Open `Settings` → `Basic Settings` → `Miscellaneous` and tick `Use proxy`. In Simple mode, `Use proxy` is in the `Network Settings` section of the `Common` tab. When `System Proxy` is on, Throne already sends its own requests through the proxy.
3. Click `Generate Warp Config` again.

If no profile is running while `Use proxy` or `System Proxy` is on, generation fails with "Request with proxy but no profile started."

Throne sends such requests to its local proxy port. Since 1.4.0, when `Disable Mixed Inbound` is on, Throne sends them directly instead, and TUN mode carries them through the running profile. If TUN mode is off too, generation fails with "Request with proxy but the mixed inbound is disabled and Tun Mode is off."

With the [kill switch](@/advanced/kill_switch.md) on, Throne's own requests are blocked unless they go through Throne. Generate the config while a profile runs in TUN mode, or with `Use proxy` on.

`Registration Domains…` holds the domains of the Cloudflare API, one per line. Throne tries them in order and uses the first one that accepts the registration. An empty list means `api.cloudflareclient.com`.

## Turn WARP on {#enable}

Click `Routing` → `Enable Warp`. A tick shows that WARP is on. Click it again to turn WARP off. A running profile restarts with the new setting.

You can also tick `Enable Warp` on the `Warp` tab and click `OK`. Then start the profile again to apply the change.

If WARP is on but the current mode has no generated config (in `AmneziaWG` mode, also when the signature is missing), the profile does not start, and Throne shows "Warp is enabled but its config has not been generated. Please generate the Warp config first in Routing Settings." In Simple mode, `Routing Settings` also shows "WARP has no config yet: click Generate Warp Config." while `Enable Warp` is ticked and the current mode has no config.

To check that WARP works, open a website that shows your IP address. It should show a Cloudflare address.

## Skip WARP for some traffic {#warp-bypass}

`warp-bypass` is an outbound that uses your profile without the WARP hop. Use it in a routing profile:

| Goal | `Default outbound` | Rules |
| --- | --- | --- |
| Most traffic through WARP, some sites without it | `proxy` | Put those sites in the `Warp-bypass` box. |
| Most traffic without WARP, some sites through it | `warp-bypass` | Put those sites in the `Proxy` box. |

The `Default outbound` list and the `Warp-bypass` box are in the routing profile editor: `Routing Settings` → `Route` tab → select a profile → `Edit`. Advanced rules can also use `warp-bypass` as their outbound. See [Routing](@/guides/routing.md#simple-rules) for the rule format.

When WARP is off, `warp-bypass` works the same as `proxy`.

## WARP as a normal profile {#warp-profiles}

You can also make WARP a profile of its own, for example to use it without another proxy, or as a hop in a chain.

1. Open `Program` → `New profile`.
2. Set `Type` to `WireGuard` or `MASQUE`.
3. Click `Generate Warp Config` (WireGuard) or `Generate WARP identity` (MASQUE). Throne fills in the keys, addresses, MTU and server address.
4. Enter a `Name` and click `OK`.

The `Mode` in `Routing Settings` does not change such a profile. To give a WireGuard WARP profile the obfuscation of `AmneziaWG` mode, tick `Enable Amnezia`, click `Amnezia Options`, and set `Junk Packet Count (Jc)` to 4, `Junk Packet Min Size (Jmin)` to 40, `Junk Packet Max Size (Jmax)` to 70, and the four magic headers H1 to H4 to 1, 2, 3 and 4. Then click `Generate QUIC Signature...`, enter `www.cloudflare.com` and click `OK`.

On its own, such a profile connects to Cloudflare directly. Keep `Enable Warp` off while you use it, or Throne adds a second WARP hop after it.

## Android {#android}

- The settings are in `Settings` → `Routing` → `WARP`: `Enable WARP`, `Mode`, the fields of each mode, `Generate WARP config` and `Registration domains`.
- The first generation asks you to accept the Cloudflare WARP terms of service.
- Since 2.0.5, `Mode` also offers `AmneziaWG`, which works as on the desktop. The app creates the signature when you choose this mode, and `Generate WARP config` creates a new one. While the signature is missing, the `Mode` row shows "AmneziaWG has no QUIC signature yet: tap Generate WARP config."
- If registration is blocked, connect first and turn on `Settings` → `Subscriptions` → `Use proxy`. In `Proxy only` mode, the request goes through the proxy too while a profile runs. While no profile runs, generation fails if `Use proxy` is on. Since 2.0.4, `Proxy only` mode alone no longer makes it fail: the request then goes out directly.
- To switch WARP quickly, open `Settings` → `Routing` → `Current routing profile`, then tap `Enable WARP` or `Disable WARP`. Before 2.0.5, the same dialog also opens from the menu (⋮) of the Profiles screen → `Routing profile`.
- If WARP is on without a generated config, Android shows "WARP is enabled but its config has not been generated. Generate it in Settings › Routing › WARP."
- The WireGuard and MASQUE profile editors have a `Generate WARP identity` button.

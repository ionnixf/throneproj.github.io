+++
title = "Proxy Chains & Custom Configs"
description = "Chain profiles, add front and landing proxies to a group, write raw sing-box or Xray JSON, and run other proxy programs as an Extra Core."
weight = 20
toc = true
+++

Chains, custom configs, Extra Core and the front and landing proxies of groups are Advanced mode only (see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)).

## Proxy chains {#chains}

A chain profile sends traffic through several profiles in a row, from top to bottom: with `A` on top and `B` below it, the path is your device → `A` → `B` → Internet. Each server sees only the one before it, and websites see the bottom one.

### Create a chain {#create-chain}

1. Open `Program` → `New profile` (`Ctrl+N`), set `Type` to `Chain Proxy` and enter a `Name`.
2. Click `Select Profile` and double-click the next hop in the main window. Repeat for every hop.
3. Drag the rows into order and click `OK`.

### Limits {#chain-limits}

- A chain cannot contain another chain or an auto selector. Auto selectors also skip chains.
- `Custom (sing-box config)` profiles cannot be hops. `Custom (sing-box outbound)` and `Custom (Xray outbound)` profiles can.
- A chain can hold one Extra Core or one `Custom (Xray config)` profile, as its top hop. A `Custom (Xray config)` hop cannot be combined with other Xray hops.
- All Xray hops must be next to each other, see [Xray in chains](@/advanced/xray.md#xray-in-chains).
- URL tests skip chains with an Extra Core or `Custom (Xray config)` hop.
- A chain can be the outbound of a routing rule, unless it has an Extra Core or full-config hop.
- If a hop was deleted, the chain does not start until you remove that hop.

## Front and landing proxies {#front-landing}

A group can add one hop before and one after every profile in it: your device → `Front Proxy` → the profile you start → `Landing Proxy` → WARP (if on) → Internet. A front proxy helps when the group's servers can only be reached through another server; a landing proxy gives the whole group the same exit.

Set them with a right-click on the group tab → `Edit selected Group`. The chain limits apply: neither can be a chain, and a group with a front proxy cannot start an Extra Core or `Custom (Xray config)` profile. If one of them was deleted, the group's profiles do not start until you change it. See also [Subscriptions & Groups](@/guides/subscriptions.md#front-landing-proxy).

## Custom configs {#custom-config}

A custom profile holds raw JSON for the core, for options the normal editors do not offer. `Program` → `New profile` has four types:

- `Custom (sing-box outbound)`: one sing-box outbound or endpoint.
- `Custom (sing-box config)`: a complete sing-box config.
- `Custom (Xray outbound)`: one Xray outbound.
- `Custom (Xray config)`: a complete Xray config.

`Json Editor` checks sing-box JSON against the schema. To start from the JSON that Throne generates for a working profile or chain, right-click it → `Share` → `Export config`. A minimal `Custom (sing-box outbound)`:

```json
{
  "type": "socks",
  "server": "203.0.113.10",
  "server_port": 1080
}
```

Imported JSON becomes normal profiles (see [Import formats](@/reference/protocols.md#import-formats)), never a `Custom (sing-box config)`. Create that type by hand.

### Full configs {#full-configs}

- **`Custom (sing-box config)`** runs exactly as written. Throne adds no inbounds, TUN, routing profile, DNS settings, WARP, or front and landing proxies, so your config needs its own inbounds.
- **`Custom (Xray config)`** gets a local SOCKS inbound with sing-box in front of it, so TUN mode, the routing profile and DNS settings still apply. Inside Xray, your config's `routing` chooses the outbound.

Full configs cannot be the outbound of a routing rule or a routing-profile endpoint. The auto selector can use `Custom (Xray config)` profiles but skips `Custom (sing-box config)` ones.

## Extra Core {#extra-core}

An Extra Core profile runs another proxy program on your computer, for a protocol Throne does not support. The program must open a local SOCKS server, which Throne connects to. Create one with `Program` → `New profile` → `Type` `Extra Core`:

| Field | What it does |
| --- | --- |
| `Core path` | The program to run. |
| `Args` | Command-line arguments. `%s` becomes the path of a temporary file with the `Config` text. Quote values with spaces, as in a shell. |
| `Config` | The program's config file. It reaches the program only if `Args` contains `%s`. |
| `Socks address`, `Socks port` | The program's SOCKS server (default `127.0.0.1`). The port must match the program's config and differ from Throne's own listen port. |
| `No logs` | Hides the program's output from the `Logs` tab. |

For example, if the program reads its config with `-c <file>`, set `Args` to `-c %s` and paste the config into `Config`.

The program starts and stops with the profile; if it exits by itself, Throne stops the profile. It runs with normal user rights, even when Throne's core runs as administrator or root for TUN mode. An Extra Core profile cannot be the outbound of a routing rule, and URL tests and the auto selector skip it.

## Android {#android}

- Chains: `Add profile` → `Manual settings` → `Proxy chain`, with the same hop order and limits.
- Groups have the same `Front proxy` and `Landing proxy` options.
- `Custom config` has the same four types, chosen in `Config type`.
- Extra Core is desktop only. Android keeps Extra Core profiles from a desktop backup but cannot start them.

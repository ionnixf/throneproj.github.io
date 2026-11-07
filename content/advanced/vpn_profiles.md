+++
title = "OpenVPN, OpenConnect & Tailscale"
description = "Import and set up OpenVPN, OpenConnect and Tailscale profiles, sign in with one-time codes, and run a work VPN next to your proxy."
weight = 50
toc = true
+++

Throne connects to OpenVPN servers and to Cisco AnyConnect and compatible VPNs (OpenConnect), and joins Tailscale networks. The VPN clients are built in, so you do not need to install OpenVPN or OpenConnect.

## OpenVPN {#openvpn}

Import a `.ovpn` file with `Program` → `Add profile from File(s)` (`Ctrl+O`), or drag it onto the main window.

- Only client profiles with `dev tun` work. TAP profiles and server configs are rejected.
- PKCS#12 bundles are not supported. Export the CA, certificate and key as PEM and paste them into `CA Certificate`, `Client Certificate` and `Client Key`.
- If the file reads the login from a separate file (`auth-user-pass`), enter `Username` and `Password` in the profile.
- Options that Throne cannot use are listed in the log.

## OpenConnect {#openconnect}

`Flavor` selects the server type: empty or `anyconnect` (Cisco AnyConnect and compatible), `gp` (Palo Alto GlobalProtect), `fortinet`, `f5` (F5 BIG-IP), `pulse` (Pulse Connect Secure) or `nc` (Juniper Network Connect).

Throne imports AnyConnect XML profiles (one profile per server), `openconnect` command lines such as `openconnect --protocol=gp --user=alice vpn.example.com`, and config files with a `protocol=` line, from a file, the clipboard or a subscription.

`Auth Group` preselects a group, realm or gateway. Some GlobalProtect, F5 and Fortinet portals need the path part of their URL in `Server Path`.

## Options for both VPN types {#vpn-options}

- **`Only route advertised network`** (on by default): only traffic to the networks that the VPN server advertises enters the tunnel; other traffic that reaches the profile is blocked. Turn it off for a VPN that should carry all traffic. An `.ovpn` file with `redirect-gateway` turns it off on import.
- **`Tunnel DNS`**: `Prefer` (default) resolves only the names that the server claims (its split-DNS domains) through the VPN's DNS servers. `Strict` also sends every remote DNS query there, so names they cannot answer fail. `None` ignores them.

## Signing in {#sign-in}

Throne signs in with the saved `Username` and `Password`. When the server asks for more, a `VPN Authentication` window opens:

- **A question or form**, such as a second password: fill it in and click `Submit`.
- **A sign-in page:** click `Open in Browser`, sign in there, then close the window.
- **Single sign-on that works only inside a browser** is not supported yet. Use a username and password, or for OpenConnect a session `Cookie` in the profile's `Advanced` settings.

If the server rejects the saved login, Throne asks for one for this session only. The `Runtime Stats` tab (Advanced mode only, see [Simple and Advanced mode](@/get_started/configuration.md#interface-mode)) shows the state of each VPN connection.

## OTP Manager {#otp-manager}

Many work VPNs ask for a one-time code from an authenticator app. Throne has its own: `Tools` → `OTP Manager` (Advanced mode only).

- **Add an entry** with `Import` → `From link or text...` (`otpauth://` links, Google Authenticator's `otpauth-migration://` export, JSON or a Base32 secret), `Add manually...`, `From clipboard` or `From QR image file...`. `Scan QR Code` finds a QR code on your screen.
- **Copy a code** by clicking its entry, or from `OTP Codes` in the tray menu.
- `Delete` removes an entry for good.

OTP entries are part of [backups](@/guides/backup.md#backup-restore) (`OTP profiles`), so keep backup files private.

### Use a code with a VPN profile {#bind-otp}

1. Open the OpenVPN or OpenConnect profile and choose the entry in `OTP`.
2. If the server expects the code as part of the password, put `{otp}` where the code goes, such as `MyPassword{otp}`. For OpenConnect, `{otp}` also works in the software token and form fields.
3. Click `OK`.

Throne then fills in a fresh code on every connect and answers the server's code prompts by itself. For an OpenVPN static challenge, enter its text in `Static Challenge`.

## Split tunnel: a work VPN next to your proxy {#split-tunnel}

A routing profile can start OpenVPN or OpenConnect profiles next to the profile you run. Traffic to the networks that the VPN advertises goes through the VPN; everything else follows the routing profile.

1. Open `Routing` → `Routing Settings` → `Route`, select the routing profile you use and click `Edit`.
2. On the `Endpoints` tab (Advanced mode only), choose the VPN profile and click `Add`, then `OK`. The tab appears once you have an OpenVPN or OpenConnect profile.
3. Make sure this routing profile is active, then start your normal profile. The VPN starts with it.

Each endpoint adds a `… route prefer` rule to the `Advanced` tab. You can only move it: rules above it are checked first.

- A profile used as an endpoint cannot also be the profile you start, or a hop of it.
- An endpoint can be a chain whose exit is an OpenVPN or OpenConnect profile. `Allow routing to inner hops` also routes to such hops inside the chain.
- Endpoint hops cannot run on Xray, and cannot be Extra Core or full-config profiles.

For a complete example, see [Recipes](@/guides/recipes.md#corporate-vpn).

## Tailscale {#tailscale}

A Tailscale profile joins your tailnet, so you can reach your Tailscale devices or use one as an exit node. Create one with `Program` → `New profile` → `Type` `Tailscale`:

| Field | What it does |
| --- | --- |
| `Auth key` | The key to log in to your tailnet, from the Tailscale admin console. |
| `Control URL` | Change it only for a self-hosted control server. |
| `Hostname` | This device's name in the tailnet. |
| `Exit node` | A tailnet device to send internet traffic through. `Exit node allow lan access` keeps your local network reachable. |
| `Accept routes` | Use the subnet routes that other devices advertise. |
| `Advertise exit node`, `Advertise routes` | Offer this computer as an exit node, or offer subnets (comma-separated). |
| `Ephemeral` | The tailnet removes this device after it goes offline. |
| `State directory`, `Global DNS` | Where the login is stored (`$HOME/.tailscale`), and whether Tailscale's DNS also uses the tailnet's global name servers. |

Tailnet names (under `ts.net`) are resolved with Tailscale's DNS, other names with your normal DNS settings. URL tests and the auto selector skip Tailscale profiles.

## Android {#android}

- OpenVPN and OpenConnect profiles work, with the same import formats, but without OTP, `Only route advertised network` and `Tunnel DNS`. They must sign in with the saved username and password. OTP entries from a desktop backup are not restored.
- Routing-profile endpoints (split tunnel) do not run. A routing profile from a desktop backup keeps them, but Android does not start them.
- Tailscale profiles are not supported. On both platforms, the auto selector skips OpenVPN and OpenConnect profiles.

+++
title = "Web UI"
description = "Manage Throne from a browser with thronectl web: set it up, reach it from another device and keep it safe."
weight = 25
toc = true
+++

`thronectl web` serves a web UI for headless Throne, and for a desktop Throne that has the [Throne API](@/advanced/api.md) on. It is a small web server, the gateway, that is part of `thronectl`: it talks to Throne through the API with the same address and key as the other `thronectl` commands.

## Quick start {#quick-start}

Run the gateway on Throne's own device, where Throne answers all its requests, [admin requests](@/headless/thronectl.md#remote) included.

### Docker {#docker}

The Docker image runs the gateway in the same container when `THRONE_WEB_LISTEN` is set. In the [`compose.yaml`](https://github.com/throneproj/Throne/blob/dev/docker/compose.yaml), add to the service `throne`:

```yaml
services:
  throne:
    environment:
      THRONE_WEB_LISTEN: "*:9096"
    ports: ["2080:2080", "9096:9096"]
```

On a host that the internet can reach, publish only the LAN address, such as `"192.168.1.2:9096:9096"`. With `docker run`, add `-e THRONE_WEB_LISTEN='*:9096' -p 9096:9096`.

```bash
docker compose up -d
docker compose logs throne
```

The log shows the password and the certificate's fingerprint. Open `https://<host>:9096/`, [check the fingerprint](#lan) and sign in.

The gateway keeps its state in `/var/lib/throne/web` on the data volume. It restarts 3 seconds after it exits, so a wrong `THRONE_WEB_*` [option](#options) repeats in the log.

### Linux packages (systemd) {#systemd}

The `.deb` and `.rpm` packages install `throne-web.service`, which runs `thronectl web` beside `throne.service` as a dynamic user of its own, with Throne's key as a credential and its state in `/var/lib/throne-web`:

```bash
sudo systemctl enable --now throne-web
sudo journalctl -u throne-web | grep 'web UI password'
```

It listens on `127.0.0.1:9096`: open it through an [SSH tunnel](#ssh-tunnel). To change its options, uncomment them in `/etc/throne/web.env` and run `sudo systemctl restart throne-web`; upgrades keep your edits. Never put the key or a password in that file.

- The unit's user cannot read root-only files, such as a TLS key or a password file. Pass them in with `LoadCredential=` (`sudo systemctl edit throne-web`), as `/etc/throne/web.env` shows.
- A port below 1024 needs `CAP_NET_BIND_SERVICE` in `AmbientCapabilities=` and `CapabilityBoundingSet=`.

### By hand, on any system {#manual}

On Throne's device, [connect thronectl to Throne](@/headless/thronectl.md#connect) and start the gateway:

```bash
export THRONE_KEY_FILE=~/.config/throne/api-key
thronectl web
```

It prints the password once and serves `http://127.0.0.1:9096/` until you press `Ctrl+C`. For a desktop Throne, [turn the API on](@/advanced/api.md#enable) and [connect thronectl](@/advanced/api.md#connect) first.

## Reaching it from another device {#remote}

The gateway listens on `127.0.0.1:9096`, which only Throne's device can reach.

### SSH tunnel {#ssh-tunnel}

```bash
ssh -L 9096:127.0.0.1:9096 user@host
```

While the session is open, open `http://localhost:9096/` on your computer (`https://` for Docker). If port 9096 is taken there, use another local port, such as `-L 19096:127.0.0.1:9096`.

### Local network (HTTPS) {#lan}

```bash
thronectl web --listen '*:9096'
```

`*:9096` listens on every address of the device, `192.168.1.2:9096` on one. On a network address the gateway serves HTTPS. Unless you give it a certificate, it creates a self-signed one and prints its SHA-256 fingerprint at every start; the `System` page shows it too. Your browser warns that the certificate is not trusted: open the certificate's details and accept it only if its SHA-256 fingerprint matches the printed one. If they differ, something between you and the device intercepts the connection: do not sign in.

Allow port 9096 in the device's firewall, and only from your network.

- The certificate names `localhost`, the host name and the device's addresses at the time, and is valid for 10 years. For a new one, such as after the address changed, delete the `tls` folder in the state directory and restart the gateway.
- To use your own certificate, pass the chain and the private key as PEM files with `--tls-cert` and `--tls-key`. Restart the gateway after you renew them.

### Behind a reverse proxy {#reverse-proxy}

Keep the gateway on loopback and give it your host name:

```bash
thronectl web --allow-host throne.example.com
```

If the proxy runs on another device or container, the gateway has to listen on a network address with `--tls off`. Let only the proxy reach it: the password crosses it unencrypted. The proxy must:

- **Pass on the `Host` header unchanged**, or changes fail with `403 csrf`. nginx replaces it unless you set it; Caddy keeps it.
- **Accept request bodies up to 512 MiB**, the largest backup the web UI restores. nginx accepts 1 MiB unless you set `client_max_body_size`.
- **Not buffer responses**: live updates and the log are streams. The gateway's `X-Accel-Buffering: no` header turns buffering off in nginx.

nginx:

```nginx
server {
    listen 443 ssl;
    server_name throne.example.com;
    ssl_certificate     /etc/ssl/certs/throne.example.com.pem;
    ssl_certificate_key /etc/ssl/private/throne.example.com.key;

    location / {
        proxy_pass http://127.0.0.1:9096;
        proxy_set_header Host $http_host;
        client_max_body_size 512m;
    }
}
```

Caddy:

```text
throne.example.com {
    reverse_proxy 127.0.0.1:9096
}
```

Behind a proxy, every browser comes from the proxy's address, so failed sign-ins from one browser [lock out](#security) the others too.

## The password {#password}

The web UI has one password and no user accounts. At its first start the gateway creates a random password, stores its hash in the file `password` in its state directory, and prints the password once: see `docker compose logs throne`, `sudo journalctl -u throne-web | grep 'web UI password'`, or the terminal where you started `thronectl web`.

The line stays in that log, which root, the groups `adm` and `systemd-journal`, and everyone who may run `docker` can read. Change the password after the first sign-in: on the `System` page, or with `thronectl web passwd`, run as the gateway's user with the same state directory. A password needs at least 8 characters, and a change signs everyone else out.

```bash
docker compose exec throne thronectl web passwd              # Docker
sudo thronectl web passwd --state-dir /var/lib/throne-web    # systemd unit
```

**Forgotten password.** Set a new one with `thronectl web passwd`, or delete the `password` file and restart the gateway, which then prints a new one.

**Password file.** `--password-file FILE` (`THRONE_WEB_PASSWORD_FILE`) reads the password in plain text from a file, such as a Docker secret in `/run/secrets/`, instead of the stored hash. The gateway rereads it every 5 seconds and signs everyone out when it changes. To change the password, change the file.

**Sessions.** A sign-in lasts until the browser has been idle for 12 hours, and 7 days at most. Restarting the gateway signs everyone out. It keeps up to 64 sessions; one more sign-in ends the least recently used.

## What the web UI covers {#features}

| Page | What you can do |
| --- | --- |
| `Status` | The state, the running profile and the rates; connect and disconnect; TUN and the system proxy (desktop Throne only); the routing profile; running tests. |
| `Profiles` | Browse, filter, test, connect, edit and delete profiles, or connect the best of a group. Import share links, a file or a URL. Add, update and delete groups and subscriptions. |
| `Routing` | Add, edit and delete routing profiles. Turn WARP on and off, pick its mode and register it (which accepts Cloudflare's WARP terms). |
| `Traffic` | Rates, and totals by profile or by app for 24 hours, 7 days or 30 days. |
| `Connections` | The open and closed connections, with process, destination, outbound and rule; close one or all. |
| `Logs` | Throne's log as it is written; the page keeps the newest 5000 lines. |
| `Settings` | The settings that the API manages, the geo files, and the rule-sets of the running profile. |
| `Backup` | Download a backup, or upload one and restore the parts you choose. |
| `System` | The versions, whether the gateway has admin rights, the certificate's fingerprint, the password, signing out, restarting and shutting down Throne. |

Changes made in Throne's window, with `thronectl` or by a subscription update show up within seconds.

### What stays on the desktop {#desktop-only}

The web UI does not edit:

- OTP accounts,
- a group's own routing profile (for a new subscription, [`thronectl sub add`](@/headless/thronectl.md#profiles) sets it),
- the OpenVPN and OpenConnect endpoints of a routing profile,
- Extra Core profiles, and profiles with settings that only Throne's window may set, such as a link to an OTP entry,
- settings that the API does not manage.

Set these up in a desktop Throne and [move them over with a backup](@/headless/thronectl.md#from-desktop), restored on the `Backup` page or with `thronectl backup restore`. Backups with Extra Core profiles are refused.

## Options {#options}

A flag wins over its variable.

| Flag | Variable | Default and meaning |
| --- | --- | --- |
| `--listen ADDR` | `THRONE_WEB_LISTEN` | `127.0.0.1:9096`. Takes `PORT` (on `127.0.0.1`), `IP:PORT`, `[IPv6]:PORT` and `*:PORT` (every address), but no host names. |
| `--state-dir DIR` | `THRONE_WEB_STATE_DIR` | The password's hash and the self-signed certificate. Default: systemd's `$STATE_DIRECTORY`, else `~/.config/thronectl/web` on Linux, `~/Library/Application Support/thronectl/web` on macOS, `%AppData%\thronectl\web` on Windows. |
| `--tls auto\|on\|off` | `THRONE_WEB_TLS` | `auto` (default): HTTPS on a network address, plain HTTP on loopback. `on`: HTTPS on loopback too. `off`: plain HTTP; on a network address, only behind a TLS reverse proxy. |
| `--tls-cert FILE`, `--tls-key FILE` | `THRONE_WEB_TLS_CERT`, `THRONE_WEB_TLS_KEY` | Your certificate chain and private key in PEM, instead of the self-signed certificate. With `auto`, they turn HTTPS on also on loopback. |
| `--allow-host LIST` | `THRONE_WEB_ALLOW_HOST` | More host names that browsers may use, such as a reverse proxy's, separated by commas or spaces. Against DNS rebinding, the gateway answers other names with `421 Misdirected Request`: on loopback it accepts only these, `localhost`, `127.0.0.1` and `::1`; on a network address every name, unless this is set. |
| `--password-file FILE` | `THRONE_WEB_PASSWORD_FILE` | The password in plain text instead of the stored hash. See [The password](#password). |

The gateway finds Throne as every `thronectl` command does, and under systemd also reads the key from the credential `api.key`. `thronectl web` exits with code 0 when stopped with `Ctrl+C` or `SIGTERM`; 2 for a wrong option or variable, no key, a bad password file or `password` file, or a certificate that does not load; and 3 when it cannot listen or for another error.

## Security {#security}

- **The web UI is an admin client.** Whoever signs in can do what `thronectl` can, including downloading backups, which hold your profiles' credentials and OTP secrets in plain text, and shutting Throne down. Give the password only to people who may manage Throne.
- **The API key stays with the gateway.** The browser gets only a session cookie and a session token.
- **Sign-in limits.** After 5 failed sign-ins within 5 minutes from one address (for IPv6, one /64 network), the gateway refuses sign-ins from that address for 60 seconds, twice as long after each further failure, up to 15 minutes. When more than 30 sign-ins fail within a minute in all, it refuses every sign-in until that minute has passed.
- **Prefer loopback and an SSH tunnel.** On the network, use HTTPS and check the certificate's fingerprint, or use a certificate that browsers trust. Plain HTTP on a network address carries the password and the session unencrypted.
- **Run the gateway on Throne's device**, so that Throne's API can stay on loopback.

## Troubleshooting {#troubleshooting}

| Symptom | Cause and fix |
| --- | --- |
| `Throne is not reachable. Retrying…` (`throne_unreachable`) | Throne is not running, its API is off, or the gateway has the wrong address. Run `thronectl status` with the gateway's settings. |
| `Throne refuses the web UI` or `Throne refused the connection` (`throne_rejected`) | The gateway's key is wrong. Give it Throne's current key (`config/api.key` in the data directory) and restart it. From another device, 5 failed attempts from its address, also by another client with an old key, lock it out for 60 seconds. |
| `The web UI's connection to Throne lacks admin rights` (`forbidden`) | The gateway does not run on Throne's device. Run it there, or let Throne answer admin requests from the network: `--api-admin-lan` for headless Throne, `Allow management from the local network` in the desktop app. |
| `421 Misdirected Request: this host name is not accepted` | Open the gateway as `localhost` or by IP address, or add the name with [`--allow-host`](#options). |
| Behind a reverse proxy, signing in fails: `The web UI server refused the request because it came from another origin` (`403 csrf`) | The proxy changes the `Host` header: see [Behind a reverse proxy](#reverse-proxy). |
| `Too many failed attempts. Try again in … s.` (`rate_limited`) | Wait until the countdown ends. |
| `thronectl web` exits: `address already in use; --listen picks another address` | Another program, or another `thronectl web`, holds port 9096. Stop it, or pass another `--listen`. |
| Restoring a backup through nginx fails with `413` | Set `client_max_body_size 512m;`. |
| `The password comes from a password file` | Change the file that `--password-file` names. |

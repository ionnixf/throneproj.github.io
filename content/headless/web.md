+++
title = "Web UI"
description = "Manage Throne from a browser with thronectl web: set it up with Docker, the systemd unit or by hand, reach it through an SSH tunnel, HTTPS or a reverse proxy, and keep it safe."
weight = 25
toc = true
+++

`thronectl web` serves a web UI for Throne. In a browser you can see and switch the connection, pick and test profiles, import profiles and update subscriptions, follow the traffic and the log, change settings, and back up and restore. It works with headless Throne and with a desktop Throne that has the [Throne API](@/advanced/api.md) on. Throne for Android has no Throne API, so the web UI cannot manage it.

The web UI is part of `thronectl`, not of Throne. `thronectl web` is a small web server, the gateway: your browser talks to the gateway, and the gateway talks to Throne through the Throne API, with the address and the key that the other `thronectl` commands use. Throne itself serves no web page.

Use the web UI when you want to look and click, for example from a phone, or from a computer without `thronectl`. For scripts, hotkeys and automation, use the [thronectl commands](@/headless/thronectl.md). Both work at the same time.

## Quick start {#quick-start}

Pick the setup that matches how Throne runs. Each of them runs the gateway on Throne's own device, where Throne answers all its requests, [admin requests](@/headless/thronectl.md#admin-requests) included.

### Docker {#docker}

The Docker image runs the gateway beside Throne, in the same container, when `THRONE_WEB_LISTEN` is set. In the [`compose.yaml`](https://github.com/throneproj/Throne/blob/dev/docker/compose.yaml) from the Throne repository, add the variable to the service `throne` and publish the port:

```yaml
services:
  throne:
    environment:
      THRONE_WEB_LISTEN: "*:9096"
    ports: ["2080:2080", "9096:9096"]
```

On a host that the internet can reach, publish only the LAN address, for example `"192.168.1.2:9096:9096"`. With `docker run`, add `-e THRONE_WEB_LISTEN='*:9096' -p 9096:9096`. Then start the container and read its log:

```bash
docker compose up -d
docker compose logs throne
```

The log shows the password and the fingerprint of the self-signed certificate. Open `https://<host>:9096/`, compare the fingerprint as described in [Local network](#lan), and sign in.

- The container's entrypoint starts `thronectl web` beside Throne as soon as Throne's API answers, and finds the API's address and key itself. When the gateway exits, the entrypoint starts it again 3 seconds later, so a wrong `THRONE_WEB_*` value shows up in the log every 3 seconds. The gateway also restarts with the container.
- The gateway keeps its state in `/var/lib/throne/web` on the data volume.
- The other `THRONE_WEB_*` variables, such as `THRONE_WEB_TLS`, `THRONE_WEB_ALLOW_HOST` or `THRONE_WEB_PASSWORD_FILE` (see [Options](#options)), go in the same `environment:` block.

### Linux packages (systemd) {#systemd}

The `.deb` and `.rpm` packages install `thronectl` as `/usr/bin/thronectl`, and a second unit, `throne-web.service`, which runs `thronectl web` beside `throne.service`. It does not run as root: systemd gives it a dynamic user of its own, passes it Throne's key `/var/lib/throne/config/api.key` as a credential, and keeps its state in `/var/lib/throne-web`. The package does not enable it:

```bash
sudo systemctl enable --now throne-web
sudo journalctl -u throne-web | grep 'web UI password'
```

Until Throne's first start has created the key file, the unit fails, and systemd starts it again every 5 seconds, as after every other failure. It listens on `127.0.0.1:9096`: open it through an [SSH tunnel](#ssh-tunnel). To change its options, uncomment the variables in `/etc/throne/web.env` (see [Options](#options)) and run `sudo systemctl restart throne-web`. Upgrades keep your edits. Never put the key or a password in this file. The unit's user cannot read files that only root can read, such as a key in another place, a TLS key or a password file; `/etc/throne/web.env` shows how to pass them in with `LoadCredential=` (`sudo systemctl edit throne-web`). A port below 1024 needs `CAP_NET_BIND_SERVICE` in the unit's `AmbientCapabilities=` and `CapabilityBoundingSet=`.

### By hand, on any system {#manual}

Install `thronectl` on the device where Throne runs (see [Install thronectl](@/headless/thronectl.md#install-thronectl)), connect it to Throne as for every other command (see [Connect thronectl to Throne](@/headless/thronectl.md#connect)), and start the gateway:

```bash
export THRONE_KEY_FILE=~/.config/throne/api-key
thronectl web
```

It prints the password once, and where it serves:

```text
thronectl web: created the web UI password: k7mfq-2xhpa-9tnwe-c3rjd
thronectl web: change it with 'thronectl web passwd'
thronectl web: serving on http://127.0.0.1:9096/ for Throne at 127.0.0.1:9095
```

Open `http://127.0.0.1:9096/` in a browser on the same device and sign in with that password. `thronectl web` runs until you press `Ctrl+C`. It starts without Throne, too: until Throne answers, the web UI says that Throne is not reachable.

For a desktop Throne, turn the API on first (`Settings` → `Integration Settings` → `Throne API`, see [Turn on the API in Throne](@/advanced/api.md#enable)) and keep its key in a file, as described in [Connect thronectl to Throne](@/advanced/api.md#connect). On Windows, in PowerShell:

```powershell
$env:THRONE_KEY_FILE = "$HOME\throne-api-key.txt"
thronectl web
```

## Reaching it from another device {#remote}

By default the gateway listens on `127.0.0.1:9096`, which only programs on Throne's device can reach. From another device, use an SSH tunnel, HTTPS on the local network, or a reverse proxy.

### SSH tunnel {#ssh-tunnel}

If you can log in to the device with SSH, keep the gateway on loopback and tunnel to it from your computer:

```bash
ssh -L 9096:127.0.0.1:9096 user@host
```

While that session is open, open `http://localhost:9096/` on your computer (`https://` for the Docker setup, which serves HTTPS). Nothing on the device's network can reach the gateway, and SSH encrypts the traffic. If port 9096 is taken on your computer, pick another local port: `ssh -L 19096:127.0.0.1:9096 user@host`, then `http://localhost:19096/`.

### Local network (HTTPS) {#lan}

To open the web UI directly from other devices on your network, let the gateway listen on a network address:

```bash
thronectl web --listen '*:9096'
```

`*:9096` listens on every address of the device, `192.168.1.2:9096` on one. On a network address the gateway serves HTTPS. Unless you give it a certificate, it creates a self-signed one in its state directory, and prints the certificate's fingerprint at every start:

```text
thronectl web: created a self-signed TLS certificate in /home/you/.config/thronectl/web/tls
thronectl web: TLS certificate SHA-256 fingerprint: 3F:A1:5C:…:9C
thronectl web: serving on https://localhost:9096/ for Throne at 127.0.0.1:9095
thronectl web: serving on https://192.168.1.2:9096/ for Throne at 127.0.0.1:9095
```

Your browser warns that the certificate is not trusted, because no certificate authority signed it. Before you accept it:

1. Open the certificate's details from the warning page or the address bar.
2. Compare its SHA-256 fingerprint with the one the gateway printed.
3. Accept the certificate only if the two match. If they differ, something between you and the device intercepts the connection: do not sign in.

The `System` page of the web UI also shows the fingerprint. Allow port 9096 in the device's firewall, and only from your network.

- The certificate names `localhost`, the device's host name and the addresses the device had when the gateway created it. To create a new one, for example after the device's address changed, delete the `tls` folder in the state directory and restart the gateway. The fingerprint changes with it.
- The self-signed certificate is valid for 10 years. When it has expired or cannot be read, the gateway creates a new one at its next start.
- To use a certificate of your own, give the certificate chain and the private key as PEM files with `--tls-cert` and `--tls-key`. The gateway reads them at start; restart it after you renew them.

### Behind a reverse proxy {#reverse-proxy}

A reverse proxy such as nginx or Caddy can serve the web UI under a host name of yours, with a certificate that browsers trust. Keep the gateway on loopback, where it serves plain HTTP that only programs on the device reach, and give it the host name:

```bash
thronectl web --allow-host throne.example.com
```

On loopback the gateway answers only requests for `localhost`, `127.0.0.1`, `::1` and the names in `--allow-host` (see [Host check](#host-check)). When the proxy runs on another device or in another container, the gateway has to listen on a network address: add `--tls off` there, and let only the proxy reach that address, because the password and the session cross it unencrypted.

The proxy must:

- **Pass on the `Host` header as the browser sent it.** The gateway refuses changes whose `Origin` header does not match the `Host` header (`403 csrf`). nginx replaces the header unless you set it; Caddy keeps it.
- **Accept request bodies up to 512 MiB**, the largest backup the web UI restores. nginx accepts 1 MiB unless you set `client_max_body_size`.
- **Pass on responses as they come.** The web UI receives live updates and the log as a stream. The gateway sends `X-Accel-Buffering: no`, which turns nginx's buffering off for them.

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

Behind a proxy, every browser reaches the gateway from the proxy's address, so failed sign-ins from one browser [lock out](#security) the others too.

## The password {#password}

The web UI has one password and no user accounts. At its first start the gateway creates a random password, stores its hash (argon2id) in the file `password` in its state directory, and prints the password once:

```text
thronectl web: created the web UI password: k7mfq-2xhpa-9tnwe-c3rjd
thronectl web: change it with 'thronectl web passwd'
```

| Setup | Where to find the first password |
| --- | --- |
| Docker | `docker compose logs throne` |
| systemd unit | `sudo journalctl -u throne-web \| grep 'web UI password'` |
| By hand | The terminal where you started `thronectl web` |

The line stays in that log, so change the password after you sign in for the first time:

- On the `System` page of the web UI. This signs out every other browser.
- With `thronectl web passwd`, run as the user the gateway runs as and with the same state directory (`--state-dir` or `THRONE_WEB_STATE_DIR`). On a terminal it asks twice without showing what you type; otherwise it reads one line from standard input. A running gateway notices the new password within 5 seconds and signs everyone out. In the Docker setup: `docker compose exec throne thronectl web passwd`.

A password needs at least 8 characters. Under systemd, set it as root; the file stays with the unit's user:

```bash
sudo thronectl web passwd --state-dir /var/lib/throne-web
```

**Forgotten password.** Set a new one with `thronectl web passwd`, or delete the `password` file in the state directory and restart the gateway. It then creates a new password and prints it.

**Password file.** `--password-file FILE` (or `THRONE_WEB_PASSWORD_FILE`) takes the password in plain text from a file instead of the stored hash, for example from a Docker secret. One line break at the end is ignored; an empty file is an error. The gateway checks the file every 5 seconds and signs everyone out when it changes. If the file turns empty or unreadable, the gateway keeps the last password and says so in its log. With a password file the `System` page cannot change the password, and neither can `thronectl web passwd` while `THRONE_WEB_PASSWORD_FILE` is set: change the file instead.

```yaml
services:
  throne:
    environment:
      THRONE_WEB_LISTEN: "*:9096"
      THRONE_WEB_PASSWORD_FILE: /run/secrets/throne_web_password
    secrets: [throne_web_password]
secrets:
  throne_web_password:
    file: ./web-password.txt
```

**Sessions.** A sign-in lasts until the browser has been idle for 12 hours, and 7 days at most. `Sign out` on the `System` page ends it. The gateway keeps sessions in memory, so restarting it signs everyone out. It keeps up to 64 sessions; one more sign-in ends the session that was used least recently.

## What the web UI covers {#features}

| Page | What you can do |
| --- | --- |
| `Status` | See the state, the running profile, the kill switch and the current rates; connect and disconnect; turn TUN and the system proxy on and off (the system proxy only in a desktop Throne); pick the routing profile (the list shows the one in use, which can be the [own routing profile](@/guides/routing.md#group-routing) of the running profile's group); restart the profile when a setting needs it; follow and cancel tests. |
| `Profiles` | Browse groups and profiles with their test results, filter and sort them; connect a profile or the best one of a group; test latency, IP and speed; clear results; delete profiles. Import share links, a file or a URL. Add groups and subscriptions, update and delete them. |
| `Traffic` | The rates of the last two minutes, and totals by profile or by app for 24 hours, 7 days or 30 days. |
| `Logs` | Throne's log as it is written, with pause, a text filter, times, copying, and colors for warnings and errors. The page keeps the newest 5000 lines. |
| `Settings` | The settings that the API manages, the geo files, and the rule-sets of the running profile. |
| `Backup` | Download a backup of the parts you choose. Upload an archive, see what it holds and restore the parts you choose; Throne restarts, and the web UI waits for it. |
| `System` | The versions of Throne and the gateway, whether the gateway has admin rights, the certificate's fingerprint, the password, signing out, restarting Throne and shutting it down. |

Changes made elsewhere, in Throne's window, with `thronectl` or by a subscription update, show up in the web UI within seconds: the gateway asks Throne what changed (`GET /v1/events`), and the page reloads only that.

### What stays on the desktop {#desktop-only}

The web UI does not edit:

- the rules of routing profiles (it only activates a routing profile),
- a group's own routing profile and `Use the subscription's routing profile` (for a new subscription, [`thronectl sub add`](@/headless/thronectl.md#profiles) sets them),
- WARP and OTP accounts,
- a profile's own settings, such as its server, ports or TLS,
- the live list of connections,
- settings that the API does not manage.

Set these up in a desktop Throne and move them over with a backup: create it in the desktop app, then restore it on the `Backup` page, or with `thronectl backup restore` as described in [Moving a desktop setup](@/headless/thronectl.md#from-desktop). Throne refuses archives with profiles that would run programs or read files on its device, such as Extra Core profiles (`invalid_archive`).

## Options {#options}

```text
thronectl web [--listen ADDR] [--state-dir DIR] [--tls auto|on|off] [--tls-cert FILE --tls-key FILE]
              [--allow-host LIST] [--password-file FILE]
thronectl web passwd [--state-dir DIR]
```

Each option has an environment variable. A flag wins over its variable.

| Flag | Variable | Default and meaning |
| --- | --- | --- |
| `--listen ADDR` | `THRONE_WEB_LISTEN` | `127.0.0.1:9096`. Takes `PORT` (on `127.0.0.1`), `IP:PORT`, `[IPv6]:PORT` and `*:PORT` (every address), as Throne's `--api-listen` does. Host names are not accepted. |
| `--state-dir DIR` | `THRONE_WEB_STATE_DIR` | The folder for the password's hash and the self-signed certificate, which the gateway creates readable only by its user. Default: systemd's `$STATE_DIRECTORY`, else `thronectl/web` in your configuration folder: `~/.config/thronectl/web` on Linux, `~/Library/Application Support/thronectl/web` on macOS, `%AppData%\thronectl\web` on Windows. |
| `--tls auto\|on\|off` | `THRONE_WEB_TLS` | `auto` (default): HTTPS on a network address, plain HTTP on loopback. `on`: HTTPS on loopback too. `off`: plain HTTP; on a network address, only behind a TLS reverse proxy. `off` together with a certificate is an error. |
| `--tls-cert FILE`, `--tls-key FILE` | `THRONE_WEB_TLS_CERT`, `THRONE_WEB_TLS_KEY` | Your certificate chain and private key in PEM, instead of the self-signed certificate. Give both. With `auto`, they turn HTTPS on also on loopback. |
| `--allow-host LIST` | `THRONE_WEB_ALLOW_HOST` | More host names that browsers may use to reach the gateway, separated by commas or spaces, such as the name of a reverse proxy. See [Host check](#host-check). |
| `--password-file FILE` | `THRONE_WEB_PASSWORD_FILE` | The password in plain text, such as a Docker secret, instead of the stored hash. See [The password](#password). |

The gateway finds Throne as every `thronectl` command does: the address from `--addr` or `THRONE_ADDR` (default `127.0.0.1:9095`), the key from `--key-file`, `THRONE_KEY` or `THRONE_KEY_FILE`, or under systemd from the credential `api.key` (`LoadCredential=api.key:/path/to/key`). `--timeout` does not apply to it.

`thronectl web` exits with:

| Code | When |
| --- | --- |
| `0` | It was stopped with `Ctrl+C` or `SIGTERM`. It shuts down within 5 seconds. |
| `2` | A wrong option or variable, no key, an empty or unreadable password file, a damaged `password` file, or a certificate that does not load. |
| `3` | It cannot listen, for example because another program holds the port, or another error. |

### Host check {#host-check}

A web page can make your browser send requests to the gateway under a host name of its own (DNS rebinding). The gateway therefore checks the host name of every request:

- On a loopback address it accepts `localhost`, `127.0.0.1`, `::1` and the names in `--allow-host`.
- On a network address it accepts every name, unless `--allow-host` is set. Then it accepts those names, the loopback names and IP addresses.

It answers other names with `421 Misdirected Request`.

## Security {#security}

- **The web UI is an admin client.** Whoever signs in can do what `thronectl` can, admin requests included: import and delete profiles, change settings, download backups, which hold your profiles' credentials and OTP secrets in plain text, restore backups, and restart or shut down Throne. Give the password only to people who may manage Throne.
- **The API key stays with the gateway.** The browser never receives it. It gets only a session cookie and a session token, and every request needs both.
- **Sign-in limits.** After 5 failed sign-ins within 5 minutes from one address (for IPv6, one /64 network), the gateway refuses sign-ins from that address for 60 seconds, twice as long after each further failure, up to 15 minutes. When more than 30 sign-ins fail within a minute in all, it refuses every sign-in until that minute has passed. A successful sign-in clears its address's failures. A failed sign-in takes at least half a second, and the gateway's log names locked-out addresses.
- **The first password stays in the log.** The systemd journal can be read by root and by members of groups such as `adm` or `systemd-journal`; the container's log by everyone who may run `docker`. Change the password after the first sign-in.
- **Prefer loopback and an SSH tunnel.** On the network, use HTTPS and check the certificate's fingerprint, or use a certificate that browsers trust. Plain HTTP on a network address carries the password and the session unencrypted; the gateway warns about it at start.
- **Run the gateway on Throne's device.** Throne's API can then stay on loopback. A gateway on another device needs the API on the network and, for admin requests, `--api-admin-lan`.

## Troubleshooting {#troubleshooting}

| Symptom | Cause and fix |
| --- | --- |
| `Throne is not reachable. Retrying…` (`throne_unreachable`) | Throne is not running, its API is off, or the gateway has the wrong address. Check `--addr` or `THRONE_ADDR`, and run `thronectl status` with the gateway's settings. The web UI tries again every 2 seconds and reloads when Throne answers. |
| `Throne refuses the web UI` or `Throne refused the connection` (`throne_rejected`) | The gateway's key is wrong. Give the gateway Throne's current key (`config/api.key` in headless Throne's data directory) and restart it. A gateway on another device can also be refused for 60 seconds after 5 failed attempts from its address, for example by another client with an old key at the same address. |
| `The web UI's connection to Throne lacks admin rights` (`forbidden`): the log, imports, settings, deletions, backups, restart and shutdown fail | The gateway does not run on Throne's device, and Throne answers admin requests only from there. Run the gateway on Throne's device, or let Throne answer admin requests from the network: `--api-admin-lan` (`THRONE_API_ADMIN_LAN=1`) for headless Throne, `Allow management from the local network` in the desktop app. The `System` page shows whether the gateway has admin rights. |
| `421 Misdirected Request: this host name is not accepted` | The [host check](#host-check): you opened the gateway under a name that it does not accept. Use `localhost` or an IP address, or add the name with `--allow-host`. |
| Behind a reverse proxy, signing in fails: `The web UI server refused the request because it came from another origin` (`403 csrf`) | The proxy changes the `Host` header. Pass it on unchanged, for example with `proxy_set_header Host $http_host;` in nginx. |
| `Too many failed attempts. Try again in … s.` (`rate_limited`) | Wait until the countdown ends. See [Security](#security). |
| The browser warns about the certificate | Expected with the self-signed certificate. Compare the fingerprints as described in [Local network](#lan). |
| `thronectl web` exits: `address already in use; --listen picks another address` | Another program, or another `thronectl web`, holds port 9096. Stop it, or pass another `--listen`. |
| Restoring a backup through nginx fails with `413` | Set `client_max_body_size 512m;`. |
| The `Logs` page says `This Throne is too old to share its log with the web UI`, or changes made in Throne's window take up to 30 seconds to show | Throne is older than the gateway and lacks `GET /v1/logs` or `GET /v1/events`. Without events, the gateway reads Throne's status every 2 seconds and everything else every 30 seconds. Update Throne. |
| `The password comes from a password file` | Change the file that `--password-file` names. The gateway notices the change within 5 seconds. |

+++
title = "Web 界面"
description = "用 thronectl web 在浏览器中管理 Throne：通过 Docker、systemd 单元或手动运行它，通过 SSH 隧道、HTTPS 或反向代理访问它，并保证它的安全。"
weight = 25
toc = true
+++

`thronectl web` 为 Throne 提供一个 Web 界面。在浏览器中，你可以查看和切换连接、选择和测试配置档、导入配置档和更新订阅、查看流量和日志、更改设置，以及备份和恢复。它适用于无界面 Throne，也适用于开启了 [Throne API](@/advanced/api.zh.md) 的桌面版 Throne。Throne for Android 没有 Throne API，因此 Web 界面无法管理它。

Web 界面是 `thronectl` 的一部分，而不是 Throne 的一部分。`thronectl web` 是一个小型 Web 服务器，即网关（gateway）：浏览器与网关通信，网关再通过 Throne API 与 Throne 通信，使用的地址和密钥与其他 `thronectl` 命令相同。Throne 本身不提供任何网页。

当你想直接查看和点击时，例如在手机上或在没有 `thronectl` 的电脑上，请使用 Web 界面。脚本、快捷键和自动化请使用 [thronectl 命令](@/headless/thronectl.zh.md)。两者可以同时使用。

## 快速入门 {#quick-start}

请选择与 Throne 运行方式相符的方案。每种方案都让网关运行在 Throne 所在的设备上，Throne 会在那里响应它的所有请求，包括[管理请求](@/headless/thronectl.zh.md#admin-requests)。

### Docker {#docker}

设置了 `THRONE_WEB_LISTEN` 时，Docker 镜像会在同一个容器中、在 Throne 旁边运行网关。在 Throne 仓库的 [`compose.yaml`](https://github.com/throneproj/Throne/blob/dev/docker/compose.yaml) 中，把这个变量添加到服务 `throne`，并发布端口：

```yaml
services:
  throne:
    environment:
      THRONE_WEB_LISTEN: "*:9096"
    ports: ["2080:2080", "9096:9096"]
```

在互联网可以访问的主机上，只发布局域网地址，例如 `"192.168.1.2:9096:9096"`。使用 `docker run` 时，添加 `-e THRONE_WEB_LISTEN='*:9096' -p 9096:9096`。然后启动容器并查看它的日志：

```bash
docker compose up -d
docker compose logs throne
```

日志中会显示密码和自签名证书的指纹。打开 `https://<host>:9096/`，按照[局域网](#lan)中的说明比对指纹，然后登录。

- Throne 的 API 一开始响应，容器的入口点就会在 Throne 旁边启动 `thronectl web`，并自行找到 API 的地址和密钥。网关退出后，入口点会在 3 秒后再次启动它，因此错误的 `THRONE_WEB_*` 值会每 3 秒在日志中出现一次。容器重启时，网关也会随之重启。
- 网关把状态保存在数据卷上的 `/var/lib/throne/web` 中。
- 其他 `THRONE_WEB_*` 变量，例如 `THRONE_WEB_TLS`、`THRONE_WEB_ALLOW_HOST` 或 `THRONE_WEB_PASSWORD_FILE`（参见[选项](#options)），都放在同一个 `environment:` 块中。

### Linux 软件包（systemd） {#systemd}

`.deb` 和 `.rpm` 软件包会把 `thronectl` 安装为 `/usr/bin/thronectl`，并安装第二个单元 `throne-web.service`，它在 `throne.service` 之外运行 `thronectl web`。它不以 root 身份运行：systemd 为它分配一个专用的动态用户，把 Throne 的密钥 `/var/lib/throne/config/api.key` 作为凭据（credential）传给它，并把它的状态保存在 `/var/lib/throne-web` 中。软件包不会启用它：

```bash
sudo systemctl enable --now throne-web
sudo journalctl -u throne-web | grep 'web UI password'
```

在 Throne 首次启动创建密钥文件之前，该单元会失败，systemd 每 5 秒重新启动它一次，与其他任何失败之后一样。它监听 `127.0.0.1:9096`：请通过 [SSH 隧道](#ssh-tunnel)打开它。要更改它的选项，请取消 `/etc/throne/web.env` 中相应变量的注释（参见[选项](#options)），然后运行 `sudo systemctl restart throne-web`。升级会保留你的修改。切勿把密钥或密码写进这个文件。该单元的用户无法读取只有 root 能读取的文件，例如放在其他位置的密钥、TLS 私钥或密码文件；`/etc/throne/web.env` 说明了如何通过 `LoadCredential=` 传入它们（`sudo systemctl edit throne-web`）。低于 1024 的端口需要在该单元的 `AmbientCapabilities=` 和 `CapabilityBoundingSet=` 中加入 `CAP_NET_BIND_SERVICE`。

### 手动运行，适用于任何系统 {#manual}

在运行 Throne 的设备上安装 `thronectl`（参见[安装 thronectl](@/headless/thronectl.zh.md#install-thronectl)），像其他命令一样把它连接到 Throne（参见[将 thronectl 连接到 Throne](@/headless/thronectl.zh.md#connect)），然后启动网关：

```bash
export THRONE_KEY_FILE=~/.config/throne/api-key
thronectl web
```

它会打印一次密码，以及它的服务地址：

```text
thronectl web: created the web UI password: k7mfq-2xhpa-9tnwe-c3rjd
thronectl web: change it with 'thronectl web passwd'
thronectl web: serving on http://127.0.0.1:9096/ for Throne at 127.0.0.1:9095
```

在同一设备的浏览器中打开 `http://127.0.0.1:9096/`，用这个密码登录。`thronectl web` 会一直运行，直到你按下 `Ctrl+C`。没有 Throne 时它也能启动：在 Throne 响应之前，Web 界面会显示无法访问 Throne。

对于桌面版 Throne，请先开启 API（`设置`（Settings）→ `Integration Settings` → `Throne API`，参见[在 Throne 中开启 API](@/advanced/api.zh.md#enable)），并按照[将 thronectl 连接到 Throne](@/advanced/api.zh.md#connect) 中的说明把密钥保存到文件中。在 Windows 上，在 PowerShell 中：

```powershell
$env:THRONE_KEY_FILE = "$HOME\throne-api-key.txt"
thronectl web
```

## 从其他设备访问 {#remote}

默认情况下，网关监听 `127.0.0.1:9096`，只有 Throne 所在设备上的程序能访问。要从其他设备访问，请使用 SSH 隧道、局域网中的 HTTPS 或反向代理。

### SSH 隧道 {#ssh-tunnel}

如果你能通过 SSH 登录该设备，请让网关保持在环回地址上，并从你的电脑建立到它的隧道：

```bash
ssh -L 9096:127.0.0.1:9096 user@host
```

在该会话打开期间，在你的电脑上打开 `http://localhost:9096/`（Docker 方案通过 HTTPS 提供服务，请使用 `https://`）。设备所在网络中的任何东西都无法访问网关，并且 SSH 会加密流量。如果你电脑上的 9096 端口已被占用，请换一个本地端口：`ssh -L 19096:127.0.0.1:9096 user@host`，然后打开 `http://localhost:19096/`。

### 局域网（HTTPS） {#lan}

要从网络中的其他设备直接打开 Web 界面，请让网关监听一个网络地址：

```bash
thronectl web --listen '*:9096'
```

`*:9096` 监听设备的所有地址，`192.168.1.2:9096` 只监听一个地址。在网络地址上，网关通过 HTTPS 提供服务。除非你给它提供证书，否则它会在状态目录中创建一个自签名证书，并在每次启动时打印该证书的指纹：

```text
thronectl web: created a self-signed TLS certificate in /home/you/.config/thronectl/web/tls
thronectl web: TLS certificate SHA-256 fingerprint: 3F:A1:5C:…:9C
thronectl web: serving on https://localhost:9096/ for Throne at 127.0.0.1:9095
thronectl web: serving on https://192.168.1.2:9096/ for Throne at 127.0.0.1:9095
```

浏览器会警告该证书不受信任，因为没有证书颁发机构为它签名。在接受它之前：

1. 从警告页面或地址栏打开证书详情。
2. 将它的 SHA-256 指纹与网关打印的指纹进行比对。
3. 只有两者一致时才接受证书。如果不一致，说明你和设备之间有东西在拦截连接：不要登录。

Web 界面的 `System` 页面也会显示指纹。请在设备的防火墙中放行 9096 端口，并且只对你的网络放行。

- 证书包含 `localhost`、设备的主机名，以及网关创建证书时设备拥有的地址。要创建新证书，例如在设备地址变化后，请删除状态目录中的 `tls` 文件夹并重启网关。指纹也会随之改变。
- 自签名证书的有效期为 10 年。证书过期或无法读取时，网关会在下次启动时创建一个新证书。
- 要使用你自己的证书，请通过 `--tls-cert` 和 `--tls-key` 提供 PEM 格式的证书链和私钥文件。网关在启动时读取它们；续期后请重启网关。

### 在反向代理之后 {#reverse-proxy}

nginx 或 Caddy 等反向代理可以用你自己的主机名、以浏览器信任的证书提供 Web 界面。请让网关保持在环回地址上，它在那里通过普通 HTTP 提供服务，只有本设备上的程序能访问，并把主机名告诉它：

```bash
thronectl web --allow-host throne.example.com
```

在环回地址上，网关只响应发往 `localhost`、`127.0.0.1`、`::1` 以及 `--allow-host` 中名称的请求（参见 [Host 检查](#host-check)）。如果代理运行在另一台设备或另一个容器中，网关就必须监听网络地址：请在那里加上 `--tls off`，并且只让代理能访问该地址，因为密码和会话会以未加密的方式经过它。

代理必须：

- **按浏览器发送时的原样转发 `Host` 头。** 网关会拒绝 `Origin` 头与 `Host` 头不一致的更改请求（`403 csrf`）。除非你进行设置，否则 nginx 会替换这个头；Caddy 会保留它。
- **接受最大 512 MiB 的请求体**，这是 Web 界面可以恢复的最大备份。除非你设置 `client_max_body_size`，否则 nginx 只接受 1 MiB。
- **随到随转发响应。** Web 界面以流的形式接收实时更新和日志。网关会发送 `X-Accel-Buffering: no`，这会为这些响应关闭 nginx 的缓冲。

nginx：

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

Caddy：

```text
throne.example.com {
    reverse_proxy 127.0.0.1:9096
}
```

在代理之后，所有浏览器都从代理的地址访问网关，因此一个浏览器的失败登录也会把其他浏览器[锁在外面](#security)。

## 密码 {#password}

Web 界面只有一个密码，没有用户账户。网关首次启动时会创建一个随机密码，把它的哈希（argon2id）保存在状态目录中的 `password` 文件里，并打印一次密码：

```text
thronectl web: created the web UI password: k7mfq-2xhpa-9tnwe-c3rjd
thronectl web: change it with 'thronectl web passwd'
```

| 方案 | 在哪里找到首个密码 |
| --- | --- |
| Docker | `docker compose logs throne` |
| systemd 单元 | `sudo journalctl -u throne-web \| grep 'web UI password'` |
| 手动运行 | 你启动 `thronectl web` 的终端 |

这一行会一直留在那份日志中，所以请在首次登录后更改密码：

- 在 Web 界面的 `System` 页面中更改。这会让其他所有浏览器退出登录。
- 用 `thronectl web passwd` 更改，需要以网关运行时的用户身份、并使用相同的状态目录（`--state-dir` 或 `THRONE_WEB_STATE_DIR`）运行。在终端中，它会询问两次，且不显示你输入的内容；否则它从标准输入读取一行。正在运行的网关会在 5 秒内发现新密码，并让所有人退出登录。在 Docker 方案中：`docker compose exec throne thronectl web passwd`。

密码至少需要 8 个字符。在 systemd 下，请以 root 身份设置密码；该文件仍归单元的用户所有：

```bash
sudo thronectl web passwd --state-dir /var/lib/throne-web
```

**忘记密码。** 用 `thronectl web passwd` 设置新密码，或者删除状态目录中的 `password` 文件并重启网关。网关随后会创建一个新密码并打印出来。

**密码文件。** `--password-file FILE`（或 `THRONE_WEB_PASSWORD_FILE`）会从文件中读取明文密码，而不是使用保存的哈希，例如来自 Docker secret 的文件。末尾的一个换行符会被忽略；空文件会报错。网关每 5 秒检查一次该文件，文件改变时会让所有人退出登录。如果文件变为空或无法读取，网关会保留原来的密码，并在日志中说明。使用密码文件时，`System` 页面无法更改密码；在设置了 `THRONE_WEB_PASSWORD_FILE` 时，`thronectl web passwd` 也不行：请直接修改该文件。

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

**会话。** 只要浏览器空闲不超过 12 小时，登录就一直有效，最长 7 天。`System` 页面上的 `Sign out` 会结束会话。网关把会话保存在内存中，因此重启网关会让所有人退出登录。网关最多保留 64 个会话；再有新的登录时，会结束最久未使用的会话。

## Web 界面的功能 {#features}

| 页面 | 可以做什么 |
| --- | --- |
| `Status` | 查看状态、正在运行的配置档、Kill Switch 和当前速率；连接和断开连接；开启和关闭 TUN 与系统代理（系统代理仅适用于桌面版 Throne）；选择路由配置档（列表会显示正在使用的路由配置档，它可能是正在运行的配置档所在分组[自己的路由配置档](@/guides/routing.zh.md#group-routing)）；在某项设置需要时重启配置档；查看和取消测试。 |
| `Profiles` | 浏览分组和配置档及其测试结果，对它们进行筛选和排序；连接某个配置档或某个分组中最好的配置档；测试延迟、IP 和速度；清除结果；删除配置档。导入分享链接、文件或 URL。添加分组和订阅，更新和删除它们。 |
| `Traffic` | 最近两分钟的速率，以及按配置档或按应用统计的 24 小时、7 天或 30 天流量总计。 |
| `Logs` | 实时显示 Throne 的日志，支持暂停、文本筛选、显示时间和复制，并为警告和错误着色。页面保留最新的 5000 行。 |
| `Settings` | API 管理的设置、geo 文件，以及正在运行的配置档的规则集。 |
| `Backup` | 下载你所选部分的备份。上传归档，查看其中的内容，并恢复你所选的部分；Throne 会重启，Web 界面会等待它。 |
| `System` | Throne 和网关的版本、网关是否拥有管理权限、证书指纹、密码、退出登录、重启 Throne 以及关闭 Throne。 |

在其他地方所做的更改，例如在 Throne 窗口中、通过 `thronectl` 或由订阅更新引起的更改，会在几秒钟内显示在 Web 界面中：网关会询问 Throne 发生了什么变化（`GET /v1/events`），页面只重新加载变化的部分。

### 仍需在桌面版中完成的操作 {#desktop-only}

Web 界面不能编辑：

- 路由配置档的规则（它只能激活某个路由配置档），
- 分组自己的路由配置档和 `使用订阅提供的路由配置档`（Use the subscription's routing profile）（对于新订阅，可以用 [`thronectl sub add`](@/headless/thronectl.zh.md#profiles) 设置它们），
- WARP 和 OTP 条目，
- 配置档自身的设置，例如服务器、端口或 TLS，
- 实时连接列表，
- API 不管理的设置。

请在桌面版 Throne 中设置这些内容，然后用备份迁移过来：在桌面版中创建备份，然后在 `Backup` 页面上恢复，或者按照[迁移桌面版的配置](@/headless/thronectl.zh.md#from-desktop)中的说明用 `thronectl backup restore` 恢复。Throne 会拒绝含有会在其设备上运行程序或读取文件的配置档（例如额外核心配置档）的归档（`invalid_archive`）。

## 选项 {#options}

```text
thronectl web [--listen ADDR] [--state-dir DIR] [--tls auto|on|off] [--tls-cert FILE --tls-key FILE]
              [--allow-host LIST] [--password-file FILE]
thronectl web passwd [--state-dir DIR]
```

每个选项都有对应的环境变量。参数优先于其对应的变量。

| 参数 | 变量 | 默认值与含义 |
| --- | --- | --- |
| `--listen ADDR` | `THRONE_WEB_LISTEN` | `127.0.0.1:9096`。接受 `PORT`（在 `127.0.0.1` 上）、`IP:PORT`、`[IPv6]:PORT` 和 `*:PORT`（所有地址），与 Throne 的 `--api-listen` 相同。不接受主机名。 |
| `--state-dir DIR` | `THRONE_WEB_STATE_DIR` | 保存密码哈希和自签名证书的文件夹，网关创建它时只允许自己的用户读取。默认值：systemd 的 `$STATE_DIRECTORY`，否则为你的配置文件夹中的 `thronectl/web`：Linux 上为 `~/.config/thronectl/web`，macOS 上为 `~/Library/Application Support/thronectl/web`，Windows 上为 `%AppData%\thronectl\web`。 |
| `--tls auto\|on\|off` | `THRONE_WEB_TLS` | `auto`（默认）：在网络地址上使用 HTTPS，在环回地址上使用普通 HTTP。`on`：在环回地址上也使用 HTTPS。`off`：普通 HTTP；在网络地址上只应在带 TLS 的反向代理之后使用。`off` 与证书同时使用会报错。 |
| `--tls-cert FILE`、`--tls-key FILE` | `THRONE_WEB_TLS_CERT`、`THRONE_WEB_TLS_KEY` | 你自己的 PEM 格式证书链和私钥，用来代替自签名证书。两者都要提供。在 `auto` 下，它们在环回地址上也会开启 HTTPS。 |
| `--allow-host LIST` | `THRONE_WEB_ALLOW_HOST` | 浏览器还可以用来访问网关的其他主机名，用逗号或空格分隔，例如反向代理的名称。参见 [Host 检查](#host-check)。 |
| `--password-file FILE` | `THRONE_WEB_PASSWORD_FILE` | 明文密码，例如 Docker secret，用来代替保存的哈希。参见[密码](#password)。 |

网关查找 Throne 的方式与任何 `thronectl` 命令相同：地址来自 `--addr` 或 `THRONE_ADDR`（默认为 `127.0.0.1:9095`），密钥来自 `--key-file`、`THRONE_KEY` 或 `THRONE_KEY_FILE`，在 systemd 下也可以来自凭据 `api.key`（`LoadCredential=api.key:/path/to/key`）。`--timeout` 对它不起作用。

`thronectl web` 的退出码：

| 退出码 | 何时 |
| --- | --- |
| `0` | 它被 `Ctrl+C` 或 `SIGTERM` 停止。它会在 5 秒内关闭。 |
| `2` | 选项或变量错误、没有密钥、密码文件为空或无法读取、`password` 文件已损坏，或者证书无法加载。 |
| `3` | 它无法监听，例如因为端口被其他程序占用，或者发生了其他错误。 |

### Host 检查 {#host-check}

网页可以让你的浏览器以该网页自己的主机名向网关发送请求（DNS rebinding）。因此网关会检查每个请求的主机名：

- 在环回地址上，它接受 `localhost`、`127.0.0.1`、`::1` 以及 `--allow-host` 中的名称。
- 在网络地址上，它接受任何名称，除非设置了 `--allow-host`。设置后，它接受这些名称、环回名称和 IP 地址。

对于其他名称，它会返回 `421 Misdirected Request`。

## 安全 {#security}

- **Web 界面是一个管理客户端。** 任何登录的人都能做 `thronectl` 能做的事，包括管理请求：导入和删除配置档、更改设置、下载以明文保存配置档凭据和 OTP 密钥的备份、恢复备份，以及重启或关闭 Throne。只把密码交给允许管理 Throne 的人。
- **API 密钥只留在网关中。** 浏览器永远不会收到它。浏览器只会得到一个会话 Cookie 和一个会话令牌，每个请求都需要这两者。
- **登录限制。** 同一地址（IPv6 为同一个 /64 网络）在 5 分钟内登录失败 5 次后，网关会在 60 秒内拒绝来自该地址的登录，之后每再失败一次，拒绝时间加倍，最长 15 分钟。如果一分钟内总共有超过 30 次登录失败，它会拒绝所有登录，直到这一分钟过去。成功登录会清除其所在地址的失败记录。失败的登录至少需要半秒钟，网关的日志会列出被锁定的地址。
- **首个密码会留在日志中。** systemd 日志可以被 root 以及 `adm` 或 `systemd-journal` 等组的成员读取；容器日志可以被任何能运行 `docker` 的人读取。首次登录后请更改密码。
- **优先使用环回地址和 SSH 隧道。** 在网络上请使用 HTTPS 并核对证书指纹，或者使用浏览器信任的证书。网络地址上的普通 HTTP 会以未加密的方式传输密码和会话；网关在启动时会对此发出警告。
- **在 Throne 所在的设备上运行网关。** 这样 Throne 的 API 可以继续留在环回地址上。运行在其他设备上的网关需要 API 在网络上监听，管理请求还需要 `--api-admin-lan`。

## 故障排除 {#troubleshooting}

| 症状 | 原因与解决方法 |
| --- | --- |
| `Throne is not reachable. Retrying…`（`throne_unreachable`） | Throne 没有运行、它的 API 已关闭，或者网关的地址不对。请检查 `--addr` 或 `THRONE_ADDR`，并用网关的设置运行 `thronectl status`。Web 界面每 2 秒重试一次，Throne 响应后会重新加载。 |
| `Throne refuses the web UI` 或 `Throne refused the connection`（`throne_rejected`） | 网关的密钥不对。请把 Throne 当前的密钥（无界面 Throne 数据目录中的 `config/api.key`）交给网关，然后重启网关。运行在其他设备上的网关，还可能因其地址失败 5 次而被 Throne 拒绝 60 秒，例如同一地址上另一个使用旧密钥的客户端造成的。 |
| `The web UI's connection to Throne lacks admin rights`（`forbidden`）：日志、导入、设置、删除、备份、重启和关闭都会失败 | 网关没有运行在 Throne 所在的设备上，而 Throne 只响应来自该设备的管理请求。请在 Throne 所在的设备上运行网关，或者让 Throne 响应来自网络的管理请求：无界面 Throne 使用 `--api-admin-lan`（`THRONE_API_ADMIN_LAN=1`），桌面版使用 `允许从局域网进行管理`（Allow management from the local network）。`System` 页面会显示网关是否拥有管理权限。 |
| `421 Misdirected Request: this host name is not accepted` | [Host 检查](#host-check)：你用网关不接受的名称打开了它。请使用 `localhost` 或 IP 地址，或者用 `--allow-host` 添加该名称。 |
| 在反向代理之后无法登录：`The web UI server refused the request because it came from another origin`（`403 csrf`） | 代理更改了 `Host` 头。请原样转发它，例如在 nginx 中使用 `proxy_set_header Host $http_host;`。 |
| `Too many failed attempts. Try again in … s.`（`rate_limited`） | 等待倒计时结束。参见[安全](#security)。 |
| 浏览器对证书发出警告 | 使用自签名证书时这是正常的。请按照[局域网](#lan)中的说明比对指纹。 |
| `thronectl web` 退出：`address already in use; --listen picks another address` | 其他程序或另一个 `thronectl web` 占用了 9096 端口。请停止它，或者指定其他 `--listen`。 |
| 通过 nginx 恢复备份时失败，返回 `413` | 设置 `client_max_body_size 512m;`。 |
| `Logs` 页面提示 `This Throne is too old to share its log with the web UI`，或者在 Throne 窗口中所做的更改最多要过 30 秒才显示 | Throne 比网关旧，没有 `GET /v1/logs` 或 `GET /v1/events`。没有事件时，网关每 2 秒读取一次 Throne 的状态，每 30 秒读取一次其他所有内容。请更新 Throne。 |
| `The password comes from a password file` | 修改 `--password-file` 指定的文件。网关会在 5 秒内发现这一更改。 |

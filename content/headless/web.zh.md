+++
title = "Web 界面"
description = "用 thronectl web 在浏览器中管理 Throne：设置它、从其他设备访问它，并保证它的安全。"
weight = 25
toc = true
+++

`thronectl web` 为无界面 Throne，以及开启了 [Throne API](@/advanced/api.zh.md) 的桌面版 Throne 提供一个 Web 界面。它是一个小型 Web 服务器，即网关（gateway），属于 `thronectl` 的一部分：它通过 API 与 Throne 通信，使用的地址和密钥与其他 `thronectl` 命令相同。

## 快速入门 {#quick-start}

请在 Throne 所在的设备上运行网关，Throne 会在那里响应它的所有请求，包括[管理请求](@/headless/thronectl.zh.md#remote)。

### Docker {#docker}

设置了 `THRONE_WEB_LISTEN` 时，Docker 镜像会在同一个容器中运行网关。在 [`compose.yaml`](https://github.com/throneproj/Throne/blob/dev/docker/compose.yaml) 中，向服务 `throne` 添加：

```yaml
services:
  throne:
    environment:
      THRONE_WEB_LISTEN: "*:9096"
    ports: ["2080:2080", "9096:9096"]
```

在互联网可以访问的主机上，只发布局域网地址，例如 `"192.168.1.2:9096:9096"`。使用 `docker run` 时，添加 `-e THRONE_WEB_LISTEN='*:9096' -p 9096:9096`。

```bash
docker compose up -d
docker compose logs throne
```

日志中会显示密码和证书指纹。打开 `https://<host>:9096/`，[核对指纹](#lan)，然后登录。

网关把状态保存在数据卷上的 `/var/lib/throne/web` 中。它退出 3 秒后会重新启动，因此错误的 `THRONE_WEB_*` [选项](#options)会在日志中反复出现。

### Linux 软件包（systemd） {#systemd}

`.deb` 和 `.rpm` 软件包会安装 `throne-web.service`，它以专用的动态用户在 `throne.service` 之外运行 `thronectl web`，把 Throne 的密钥作为凭据（credential），并把状态保存在 `/var/lib/throne-web` 中：

```bash
sudo systemctl enable --now throne-web
sudo journalctl -u throne-web | grep 'web UI password'
```

它监听 `127.0.0.1:9096`：请通过 [SSH 隧道](#ssh-tunnel)打开它。要更改它的选项，请在 `/etc/throne/web.env` 中取消相应行的注释，然后运行 `sudo systemctl restart throne-web`；升级会保留你的修改。切勿把密钥或密码写进这个文件。

- 该单元的用户无法读取只有 root 能读取的文件，例如 TLS 私钥或密码文件。请通过 `LoadCredential=` 传入它们（`sudo systemctl edit throne-web`），`/etc/throne/web.env` 中有说明。
- 低于 1024 的端口需要在 `AmbientCapabilities=` 和 `CapabilityBoundingSet=` 中加入 `CAP_NET_BIND_SERVICE`。

### 手动运行，适用于任何系统 {#manual}

在 Throne 所在的设备上，[将 thronectl 连接到 Throne](@/headless/thronectl.zh.md#connect)，然后启动网关：

```bash
export THRONE_KEY_FILE=~/.config/throne/api-key
thronectl web
```

它会打印一次密码，并在 `http://127.0.0.1:9096/` 上提供服务，直到你按下 `Ctrl+C`。对于桌面版 Throne，请先[开启 API](@/advanced/api.zh.md#enable) 并[连接 thronectl](@/advanced/api.zh.md#connect)。

## 从其他设备访问 {#remote}

网关监听 `127.0.0.1:9096`，只有 Throne 所在的设备能访问。

### SSH 隧道 {#ssh-tunnel}

```bash
ssh -L 9096:127.0.0.1:9096 user@host
```

在会话打开期间，在你的电脑上打开 `http://localhost:9096/`（Docker 方案请使用 `https://`）。如果你电脑上的 9096 端口已被占用，请换一个本地端口，例如 `-L 19096:127.0.0.1:9096`。

### 局域网（HTTPS） {#lan}

```bash
thronectl web --listen '*:9096'
```

`*:9096` 监听设备的所有地址，`192.168.1.2:9096` 只监听一个地址。在网络地址上，网关通过 HTTPS 提供服务。除非你给它提供证书，否则它会创建一个自签名证书，并在每次启动时打印该证书的 SHA-256 指纹；`System` 页面也会显示它。浏览器会警告该证书不受信任：请打开证书详情，只有当它的 SHA-256 指纹与打印的指纹一致时才接受它。如果不一致，说明你和设备之间有东西在拦截连接：不要登录。

请在设备的防火墙中放行 9096 端口，并且只对你的网络放行。

- 证书包含 `localhost`、主机名以及设备当时拥有的地址，有效期为 10 年。要获得新证书，例如在地址变化后，请删除状态目录中的 `tls` 文件夹并重启网关。
- 要使用你自己的证书，请通过 `--tls-cert` 和 `--tls-key` 提供 PEM 格式的证书链和私钥文件。续期后请重启网关。

### 在反向代理之后 {#reverse-proxy}

请让网关保持在环回地址上，并把你的主机名告诉它：

```bash
thronectl web --allow-host throne.example.com
```

如果代理运行在另一台设备或另一个容器中，网关就必须用 `--tls off` 监听网络地址。请只让代理能访问该地址：密码会以未加密的方式经过它。代理必须：

- **原样转发 `Host` 头**，否则更改请求会以 `403 csrf` 失败。除非你进行设置，否则 nginx 会替换这个头；Caddy 会保留它。
- **接受最大 512 MiB 的请求体**，这是 Web 界面可以恢复的最大备份。除非你设置 `client_max_body_size`，否则 nginx 只接受 1 MiB。
- **不缓冲响应**：实时更新和日志是以流的形式传输的。网关发送的 `X-Accel-Buffering: no` 头会关闭 nginx 的缓冲。

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

在代理之后，所有浏览器都来自代理的地址，因此一个浏览器的失败登录也会把其他浏览器[锁在外面](#security)。

## 密码 {#password}

Web 界面只有一个密码，没有用户账户。网关首次启动时会创建一个随机密码，把它的哈希保存在状态目录中的 `password` 文件里，并打印一次密码：参见 `docker compose logs throne`、`sudo journalctl -u throne-web | grep 'web UI password'`，或你启动 `thronectl web` 的终端。

这一行会一直留在那份日志中，而 root、`adm` 和 `systemd-journal` 组以及任何能运行 `docker` 的人都可以读取它。首次登录后请更改密码：在 `System` 页面中，或者以网关的用户身份、使用相同的状态目录运行 `thronectl web passwd`。密码至少需要 8 个字符，更改密码会让其他所有人退出登录。

```bash
docker compose exec throne thronectl web passwd              # Docker
sudo thronectl web passwd --state-dir /var/lib/throne-web    # systemd 单元
```

**忘记密码。** 用 `thronectl web passwd` 设置新密码，或者删除 `password` 文件并重启网关，网关随后会打印一个新密码。

**密码文件。** `--password-file FILE`（`THRONE_WEB_PASSWORD_FILE`）会从文件中读取明文密码，而不是使用保存的哈希，例如 `/run/secrets/` 中的 Docker secret。网关每 5 秒重新读取一次该文件，文件改变时会让所有人退出登录。要更改密码，请修改该文件。

**会话。** 只要浏览器空闲不超过 12 小时，登录就一直有效，最长 7 天。重启网关会让所有人退出登录。网关最多保留 64 个会话；再有新的登录时，会结束最久未使用的会话。

## Web 界面的功能 {#features}

| 页面 | 可以做什么 |
| --- | --- |
| `Status` | 状态、正在运行的配置档和速率；连接和断开连接；TUN 与系统代理（仅桌面版 Throne）；路由配置档；正在进行的测试。 |
| `Profiles` | 浏览、筛选、测试、连接、编辑和删除配置档，或连接某个分组中最好的配置档。导入分享链接、文件或 URL。添加、更新和删除分组和订阅。 |
| `Routing` | 添加、编辑和删除路由配置档。开启和关闭 WARP、选择它的模式并注册它（注册时会接受 Cloudflare 的 WARP 条款）。 |
| `Traffic` | 速率，以及按配置档或按应用统计的 24 小时、7 天或 30 天流量总计。 |
| `Connections` | 已打开和已关闭的连接，包括进程、目标、出站（outbound）和规则；关闭其中一个或全部。 |
| `Logs` | 实时显示 Throne 的日志；页面保留最新的 5000 行。 |
| `Settings` | API 管理的设置、geo 文件，以及正在运行的配置档的规则集。 |
| `Backup` | 下载备份，或者上传备份并恢复你所选的部分。 |
| `System` | 版本、网关是否拥有管理权限、证书指纹、密码、退出登录、重启 Throne 以及关闭 Throne。 |

在 Throne 窗口中、通过 `thronectl` 或由订阅更新所做的更改，会在几秒钟内显示出来。

### 仍需在桌面版中完成的操作 {#desktop-only}

Web 界面不能编辑：

- OTP 条目，
- 分组自己的路由配置档（对于新订阅，可以用 [`thronectl sub add`](@/headless/thronectl.zh.md#profiles) 设置它），
- 路由配置档中的 OpenVPN 和 OpenConnect 端点（endpoints），
- 额外核心配置档，以及带有只能由 Throne 窗口设置的设置的配置档，例如关联了 OTP 条目的配置档，
- API 不管理的设置。

请在桌面版 Throne 中设置这些内容，然后[用备份迁移过来](@/headless/thronectl.zh.md#from-desktop)，在 `Backup` 页面上或用 `thronectl backup restore` 恢复。含有额外核心配置档的备份会被拒绝。

## 选项 {#options}

参数优先于其对应的变量。

| 参数 | 变量 | 默认值与含义 |
| --- | --- | --- |
| `--listen ADDR` | `THRONE_WEB_LISTEN` | `127.0.0.1:9096`。接受 `PORT`（在 `127.0.0.1` 上）、`IP:PORT`、`[IPv6]:PORT` 和 `*:PORT`（所有地址），但不接受主机名。 |
| `--state-dir DIR` | `THRONE_WEB_STATE_DIR` | 密码哈希和自签名证书。默认值：systemd 的 `$STATE_DIRECTORY`，否则 Linux 上为 `~/.config/thronectl/web`，macOS 上为 `~/Library/Application Support/thronectl/web`，Windows 上为 `%AppData%\thronectl\web`。 |
| `--tls auto\|on\|off` | `THRONE_WEB_TLS` | `auto`（默认）：在网络地址上使用 HTTPS，在环回地址上使用普通 HTTP。`on`：在环回地址上也使用 HTTPS。`off`：普通 HTTP；在网络地址上只应在带 TLS 的反向代理之后使用。 |
| `--tls-cert FILE`、`--tls-key FILE` | `THRONE_WEB_TLS_CERT`、`THRONE_WEB_TLS_KEY` | 你自己的 PEM 格式证书链和私钥，用来代替自签名证书。在 `auto` 下，它们在环回地址上也会开启 HTTPS。 |
| `--allow-host LIST` | `THRONE_WEB_ALLOW_HOST` | 浏览器还可以使用的其他主机名，例如反向代理的名称，用逗号或空格分隔。为防范 DNS rebinding，网关对其他名称返回 `421 Misdirected Request`：在环回地址上，它只接受这些名称以及 `localhost`、`127.0.0.1` 和 `::1`；在网络地址上，它接受任何名称，除非设置了此选项。 |
| `--password-file FILE` | `THRONE_WEB_PASSWORD_FILE` | 明文密码，用来代替保存的哈希。参见[密码](#password)。 |

网关查找 Throne 的方式与任何 `thronectl` 命令相同，在 systemd 下还会从凭据 `api.key` 读取密钥。`thronectl web` 被 `Ctrl+C` 或 `SIGTERM` 停止时以退出码 0 退出；选项或变量错误、没有密钥、密码文件或 `password` 文件有问题，或者证书无法加载时以 2 退出；无法监听或发生其他错误时以 3 退出。

## 安全 {#security}

- **Web 界面是一个管理客户端。** 任何登录的人都能做 `thronectl` 能做的事，包括下载以明文保存配置档凭据和 OTP 密钥的备份，以及关闭 Throne。只把密码交给允许管理 Throne 的人。
- **API 密钥只留在网关中。** 浏览器只会得到一个会话 Cookie 和一个会话令牌。
- **登录限制。** 同一地址（IPv6 为同一个 /64 网络）在 5 分钟内登录失败 5 次后，网关会在 60 秒内拒绝来自该地址的登录，之后每再失败一次，拒绝时间加倍，最长 15 分钟。如果一分钟内总共有超过 30 次登录失败，它会拒绝所有登录，直到这一分钟过去。
- **优先使用环回地址和 SSH 隧道。** 在网络上请使用 HTTPS 并核对证书指纹，或者使用浏览器信任的证书。网络地址上的普通 HTTP 会以未加密的方式传输密码和会话。
- **在 Throne 所在的设备上运行网关**，这样 Throne 的 API 可以继续留在环回地址上。

## 故障排除 {#troubleshooting}

| 症状 | 原因与解决方法 |
| --- | --- |
| `Throne is not reachable. Retrying…`（`throne_unreachable`） | Throne 没有运行、它的 API 已关闭，或者网关的地址不对。请用网关的设置运行 `thronectl status`。 |
| `Throne refuses the web UI` 或 `Throne refused the connection`（`throne_rejected`） | 网关的密钥不对。请把 Throne 当前的密钥（数据目录中的 `config/api.key`）交给网关，然后重启它。从其他设备访问时，来自其地址的 5 次失败尝试（包括另一个使用旧密钥的客户端造成的）会让它被锁定 60 秒。 |
| `The web UI's connection to Throne lacks admin rights`（`forbidden`） | 网关没有运行在 Throne 所在的设备上。请在那里运行它，或者让 Throne 响应来自网络的管理请求：无界面 Throne 使用 `--api-admin-lan`，桌面版使用 `允许从局域网进行管理`（Allow management from the local network）。 |
| `421 Misdirected Request: this host name is not accepted` | 用 `localhost` 或 IP 地址打开网关，或者用 [`--allow-host`](#options) 添加该名称。 |
| 在反向代理之后无法登录：`The web UI server refused the request because it came from another origin`（`403 csrf`） | 代理更改了 `Host` 头：参见[在反向代理之后](#reverse-proxy)。 |
| `Too many failed attempts. Try again in … s.`（`rate_limited`） | 等待倒计时结束。 |
| `thronectl web` 退出：`address already in use; --listen picks another address` | 其他程序或另一个 `thronectl web` 占用了 9096 端口。请停止它，或者指定其他 `--listen`。 |
| 通过 nginx 恢复备份时失败，返回 `413` | 设置 `client_max_body_size 512m;`。 |
| `The password comes from a password file` | 修改 `--password-file` 指定的文件。 |

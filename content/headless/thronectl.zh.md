+++
title = "使用 thronectl 管理"
description = "将 thronectl 连接到无界面 Throne，添加配置档和订阅，用备份迁移桌面版的配置，更改设置，以及从其他设备管理 Throne。"
weight = 20
toc = true
+++

无界面 Throne 没有窗口，因此你需要用 `thronectl` 来管理它，它是 [Throne API](@/advanced/api.zh.md) 的命令行客户端。本页介绍如何将 `thronectl` 连接到无界面 Throne，以及如何用它来设置 Throne。完整的命令和参数列表请参见 [Throne API 与 thronectl](@/advanced/api.zh.md)。如需在浏览器中管理 Throne，请参见 [Web 界面](@/headless/web.zh.md)。

## 安装 thronectl {#install-thronectl}

Throne 的 `.deb` 和 `.rpm` 软件包会把 `thronectl` 安装为 `/usr/bin/thronectl`，Linux ZIP 包中它位于 `Throne` 旁边。其他情况下，请把它安装在运行 Throne 的电脑上，以及你想用来管理 Throne 的其他任何电脑上：

- 从 [Throne-api 的发布页面](https://github.com/throneproj/Throne-api/releases)下载适用于 Linux、Windows、macOS 或 FreeBSD 的二进制文件，或者
- 用 Go 构建：`go install github.com/throneproj/Throne-api/cmd/thronectl@latest`。

[Docker 镜像](@/headless/installation.zh.md#docker)已经在 `/usr/local/bin` 中包含 `thronectl`。Throne 的 Windows 和 macOS 下载包中没有它。

如果想让 shell 自动补全命令、配置档 ID 和设置名称，请参见 [Shell 补全](@/advanced/api.zh.md#completion)。

## 将 thronectl 连接到 Throne {#connect}

`thronectl` 从 `THRONE_ADDR`（默认为 `127.0.0.1:9095`）读取 Throne 的地址，从 `THRONE_KEY_FILE` 指定的文件、`THRONE_KEY` 或 `--key-file` 读取密钥。它从不接受以参数形式直接给出的密钥本身。

`Throne --print-connection` 会以 `export` 行的形式打印这两个变量，但从不打印密钥本身。请以运行 Throne 的用户身份、并使用相同的 `--data-dir` 运行它：

```bash
$ sudo /opt/Throne/Throne --print-connection
export THRONE_ADDR=127.0.0.1:9095
export THRONE_KEY_FILE=/var/lib/throne/config/api.key
```

具体用法取决于 Throne 的运行方式：

- **systemd 用户单元**（Throne 以你的身份运行）：`eval "$(/opt/Throne/Throne --print-connection)"` 会设置这两个变量。然后运行 `thronectl status`。
- **系统单元、Docker 或 macOS LaunchDaemon**（Throne 以 root 身份运行）：密钥文件只有 root 能读取，而 `sudo` 会丢弃这些变量。你可以把密钥复制到一个属于你自己的文件中：

  ```bash
  mkdir -p ~/.config/throne
  sudo install -m 600 -o "$USER" /var/lib/throne/config/api.key ~/.config/throne/api-key
  export THRONE_KEY_FILE=~/.config/throne/api-key
  thronectl status
  ```

  或者以 root 身份、使用该密钥文件运行 `thronectl`：`sudo thronectl --key-file /var/lib/throne/config/api.key status`。
- **Docker：** 在容器内运行 `thronectl`：`docker compose exec throne thronectl --key-file /var/lib/throne/config/api.key status`。
- **Windows 服务：** 参见[在命令行窗口中运行命令](@/headless/installation.zh.md#windows-prompt)。

把 `export` 行放进你的 shell 启动文件（例如 `~/.bashrc`），这样每个新的 shell 都会设置它。

当 API 已关闭或未在监听，或者其密钥不在文件中时，`--print-connection` 会以退出码 2 退出。最后一种情况发生在数据目录来自一个用自己的密钥开启了 API 的桌面版 Throne 时。请把该密钥用作 `THRONE_KEY`（在桌面版中：`Integration Settings` → `Throne API` → `Copy for scripts`），或者用 `--api-key-file` 启动 Throne。

## 管理请求 {#admin-requests}

会更改 Throne 所拥有内容的请求属于管理请求：`import`、`sub add`、`group`、`profile rm`、`settings set`、`backup`、`restart` 和 `shutdown`。`logs` 也是，因为日志会列出连接访问过的网站。Throne 只响应来自其所在设备的管理请求，除非它以 `--api-admin-lan`（`THRONE_API_ADMIN_LAN=1`）运行。从其他设备发出时，这些请求会以 `forbidden` 失败。

其他所有请求，例如 `status`、`connect`、`tun on` 或 `test`，在任何允许访问 API 并持有密钥的设备上都可以使用。

## 配置档与订阅 {#profiles}

```bash
thronectl import links.txt                        # 分享链接、订阅内容、Clash、sing-box、Xray、WireGuard 或 OpenVPN
thronectl sub add 'https://sub.example.com/abc' --name Provider
thronectl groups
thronectl profiles --group 3
thronectl connect 12                              # 或：thronectl connect best --group 3
thronectl tun on
```

- `thronectl import` 读取不超过 900 KiB 的文件，或者用 `-` 读取标准输入。`--url URL` 让 Throne 下载某个 URL 一次。不带 `--group` 时，配置档会进入 Throne 中的当前分组。
- `thronectl sub add` 添加一个订阅分组并下载它；除非你提供 `--name`，否则该分组以 URL 中的主机名命名。除非你添加 `--no-auto-update`，否则它会自动更新。
- `thronectl sub add --route ID` 为新分组指定自己的路由配置档，ID 取自 `thronectl routes`。`--import-route` 保留订阅提供的路由配置档，并在每次更新时刷新它。参见[按分组设置路由配置档](@/guides/routing.zh.md#group-routing)。`thronectl groups` 会在 `ROUTE` 下显示每个分组的路由配置档。
- `thronectl connect best` 启动延迟最低的配置档，并且会先测试没有近期结果的配置档（`--max-age`，例如 `--max-age 1h`）。
- `thronectl test latency --group 3` 测试一个分组。测试可能需要几分钟；请给它们更长的 `--timeout`，例如 `thronectl --timeout 5m test latency --group 3`。

每次 Throne 启动时，正在运行的配置档都会再次启动（如果当时 TUN 处于开启状态，也会开启 TUN），直到你运行 `thronectl disconnect`。如果想在某一次启动时不启动它，请使用 [`--no-resume`](@/headless/command_line.zh.md#options)。

## 迁移桌面版的配置 {#from-desktop}

有些内容在桌面版 Throne 中设置更方便，例如路由配置档、OTP 条目或 VPN 登录。请在桌面版中设置好，然后用备份迁移过来：

```bash
# 在桌面版上：在“备份和恢复”（Backup and Restore）设置中创建备份，或者在桌面版的 Throne API 开启时运行：
thronectl backup create -o desktop.thrbackup
scp desktop.thrbackup box:

# 在目标设备上：
thronectl backup restore desktop.thrbackup
```

- `backup restore` 会显示归档中的内容，并在恢复前询问。没有终端时，请添加 `--yes`。
- 默认情况下，它会恢复配置档、路由配置档、OTP 条目和 IP 列表。如果还要恢复设置，请添加 `--parts profiles,routes,otp,ip_lists,settings`。
- 请像默认情况那样同时恢复 `profiles` 和 `routes`。如果只恢复其中之一，分组会失去自己的路由配置档，转而跟随活动的路由配置档。
- 恢复设置时，会保留属于本设备的设置：Throne API、入站地址和端口、TUN、Kill Switch、NTP、日志以及文件路径。
- 恢复后 Throne 会重启。
- 通过 API 恢复时，Throne 会拒绝含有会在其设备上运行程序或读取文件的配置档（例如额外核心配置档）的归档（`invalid_archive`）。
- 备份归档以明文保存配置档的凭据和 OTP 密钥。用完后请删除它们。

桌面版中的备份请参见[备份与恢复](@/guides/backup.zh.md#backup-restore)。

## 设置 {#settings}

`thronectl settings` 会列出 API 可以更改的设置及其当前值。`thronectl settings set` 可以更改一项或多项设置：

```bash
thronectl settings
thronectl settings get log_level
thronectl settings set kill_switch=true log_level=debug
```

`true`、`false` 和数字按原样发送，其他内容都作为文本发送。这些设置包括 `Throne --check` 和 `tun_conflict` 错误中提到的那些：TUN 的地址范围（`tun_ipv4_cidr`、`tun_ipv6_cidr`）、核心的 DNS 端口（`dns_in_port`）、入站（`inbound_address`、`inbound_port`）、Clash API（`clash_api_port`、`clash_listen_addr`）以及 sing-box API（`singbox_api_port`）。有些设置只有在配置档重新启动后才会生效；具体是哪些，请参见 API 规范中的[设置表](https://github.com/throneproj/Throne-api/blob/main/spec/api.md#settings)。

`thronectl settings set dns_in_lan=true` 让网络中的其他设备（例如路由器）可以使用核心的 DNS 服务器；参见[供其他设备使用的 DNS 服务器](@/guides/dns.zh.md#dns-server)。

API 不管理的设置只能在桌面版 Throne 中更改。请通过[恢复设置](#from-desktop)把它们迁移过来。

## 从其他设备管理 {#remote}

默认情况下，API 只监听 `127.0.0.1`。要从其他设备管理 Throne，请让它在网络上监听并设置允许列表；如果那台设备需要导入内容和更改设置，还要允许管理请求。对于 systemd 单元，请把以下内容写入 `/etc/throne/throne.env`，然后运行 `sudo systemctl restart throne`：

```bash
THRONE_API_LISTEN=*:9095
THRONE_API_ALLOW=192.168.1.0/24
THRONE_API_ADMIN_LAN=1
```

在另一台设备上，把密钥复制到一个只有你能读取的文件中，将 `THRONE_KEY_FILE` 设为该文件，并设置 `THRONE_ADDR=<box>:9095`。

- [Kill Switch](@/advanced/kill_switch.zh.md) 始终放行 API 的回复，因此无论客户端从哪里连接，都能访问 API。
- 发往 API 的流量是加密并经过认证的，在局域网中也是如此。尽管如此，除非你需要在网络上使用 API，否则请让它保持在环回地址上，并且始终提供允许列表。
- 同一地址在一分钟内认证失败 5 次后，Throne 会在 60 秒内拒绝该地址。一个使用旧密钥的客户端可能会把同一 NAT 后面的其他客户端锁在外面。
- 在 Linux 上，如果 API 监听网络地址时启动 TUN，而远程客户端可能会失去对 API 的访问，Throne 会警告一次。参见 [Throne 如何保持可访问](@/headless/recovery.zh.md#protection)。

## 命令 {#commands}

最常用的命令：

| 命令 | 作用 |
| --- | --- |
| `thronectl status` | 状态、正在运行的配置档、TUN、路由配置档、版本、能力，以及 TUN 冲突（如果有） |
| `thronectl groups`、`thronectl profiles [--group N]` | 分组和配置档，以及它们的 ID 和最近的测试结果 |
| `thronectl connect <id>`、`connect best`、`connect last` | 启动配置档 |
| `thronectl disconnect`、`thronectl toggle` | 停止配置档；连接或断开连接 |
| `thronectl tun on`、`tun off` | TUN 模式 |
| `thronectl routes`、`thronectl route <id>` | 列出并激活路由配置档 |
| `thronectl test latency \| ip \| speed --group N` | 测试配置档 |
| `thronectl sub update --all` | 更新所有订阅 |
| `thronectl geo update`、`thronectl rulesets update` | 重新下载 geo 文件和规则集 |
| `thronectl traffic --by app --since 7d` | 流量总计 |
| `thronectl logs -n 50`、`thronectl logs -f` | Throne 日志的最后几行；加上 `-f` 后会继续显示 Throne 写入的新行。参见[日志](@/headless/monitoring.zh.md#logs) |
| `thronectl restart`、`thronectl shutdown` | 重启 Throne；让它退出 |

给任何命令加上 `--json` 都可以得到适合脚本使用的 JSON 输出。`thronectl help <command>` 会说明某个命令。所有命令和参数以及 `thronectl` 的退出码，请参见 [Throne API 与 thronectl](@/advanced/api.zh.md#commands)。

`thronectl restart`、恢复备份和 `SIGHUP` 都会重启 Throne；重启方式取决于[重启模式](@/headless/command_line.zh.md#restart-modes)。`thronectl shutdown` 会让 Throne 以退出码 0 退出。在 systemd、launchd 和 Windows 服务下，它随后会保持停止状态；使用 `restart: unless-stopped` 的 Docker 则会再次启动它。

+++
title = "使用 thronectl 管理"
description = "将 thronectl 连接到无界面 Throne，添加配置档和订阅，迁移桌面版的配置，以及从其他设备管理 Throne。"
weight = 20
toc = true
+++

无界面 Throne 没有窗口。你可以用 [Throne API](@/advanced/api.zh.md) 的命令行客户端 `thronectl` 管理它，也可以在浏览器中用 [Web 界面](@/headless/web.zh.md)管理它。

## 将 thronectl 连接到 Throne {#connect}

`.deb` 和 `.rpm` 软件包会把 `thronectl` 安装为 `/usr/bin/thronectl`，Linux ZIP 包中它位于 `Throne` 旁边，Docker 镜像中它位于 `/usr/local/bin`。在 macOS、Windows 以及你用来管理 Throne 的其他电脑上，请自行[安装 thronectl](@/advanced/api.zh.md#install-thronectl)。

`thronectl` 从 `THRONE_ADDR`（默认为 `127.0.0.1:9095`）读取 Throne 的地址，从 `THRONE_KEY_FILE` 指定的文件、`THRONE_KEY` 或 `--key-file` 读取密钥。它从不接受以参数形式给出的密钥本身。`Throne --print-connection` 会打印这两个变量。请以运行 Throne 的用户身份、并使用相同的 `--data-dir` 运行它：

```bash
$ sudo /opt/Throne/Throne --print-connection
export THRONE_ADDR=127.0.0.1:9095
export THRONE_KEY_FILE=/var/lib/throne/config/api.key
```

- **systemd 用户单元：** `eval "$(/opt/Throne/Throne --print-connection)"`，然后运行 `thronectl status`。
- **Throne 以 root 身份运行**（系统单元、macOS LaunchDaemon）：只有 root 能读取密钥文件，而 `sudo` 会丢弃这些变量。请把密钥复制到一个属于你自己的文件中：

  ```bash
  mkdir -p ~/.config/throne
  sudo install -m 600 -o "$USER" /var/lib/throne/config/api.key ~/.config/throne/api-key
  export THRONE_KEY_FILE=~/.config/throne/api-key
  thronectl status
  ```

  或者以 root 身份运行 `thronectl`：`sudo thronectl --key-file /var/lib/throne/config/api.key status`。
- **Docker：** `docker compose exec throne thronectl --key-file /var/lib/throne/config/api.key status`。
- **Windows 服务：** 参见[在命令行窗口中运行命令](@/headless/installation.zh.md#windows-prompt)。

把 `export` 行放进你的 shell 启动文件，例如 `~/.bashrc`。如果想在 shell 中补全命令和 ID，请参见 [Shell 补全](@/advanced/api.zh.md#completion)。

## 常用命令 {#profiles}

```bash
thronectl status
thronectl import links.txt               # 分享链接、订阅、Clash、sing-box、Xray、WireGuard 或 OpenVPN
thronectl sub add 'https://sub.example.com/abc' --name Provider
thronectl profiles --group 3
thronectl connect 12                     # 或：thronectl connect best --group 3
thronectl tun on
thronectl routes                         # 然后：thronectl route <id>
thronectl logs -f
thronectl disconnect
```

- `import` 读取不超过 900 KiB 的文件，或者用 `-` 读取标准输入；`--url URL` 让 Throne 下载某个 URL 一次。不带 `--group` 时，配置档会进入 Throne 当前显示的分组。
- `sub add --route ID` 为新分组指定自己的[路由配置档](@/guides/routing.zh.md#group-routing)；`--import-route` 使用订阅提供的路由配置档。
- `connect best` 会先测试没有近期结果的配置档（`--max-age 1h`），然后启动延迟最低的配置档。
- 测试可能需要几分钟，所以请给它们更长的超时时间：`thronectl --timeout 5m test latency --group 3`。
- `thronectl restart` 按照 Throne 的[重启模式](@/headless/command_line.zh.md#restart-modes)重启它。`thronectl shutdown` 让它以退出码 0 退出：systemd、launchd 和 Windows 服务会让它保持停止状态，而使用 `restart: unless-stopped` 的 Docker 会再次启动它。

加上 `--json` 可以得到 JSON 输出；`thronectl help <command>` 会说明某个命令。所有命令和退出码请参见[命令](@/advanced/api.zh.md#commands)。

## 迁移桌面版的配置 {#from-desktop}

OTP 条目、VPN 登录和路由配置档在桌面版 Throne 中设置更方便。请在桌面版中设置好，然后用备份迁移过来：

```bash
# 在桌面版上：在“备份和恢复”（Backup and Restore）设置中创建备份，或者在桌面版的 Throne API 开启时运行：
thronectl backup create -o desktop.thrbackup
scp desktop.thrbackup box:

# 在目标设备上：
thronectl backup restore desktop.thrbackup
```

- `backup restore` 会显示归档中的内容，并先询问。没有终端时，请添加 `--yes`。恢复后 Throne 会重启。
- 默认情况下，它会恢复配置档、路由配置档、OTP 条目和 IP 列表。如果还要恢复设置，请添加 `--parts profiles,routes,otp,ip_lists,settings`。恢复设置时，会保留属于本设备的设置：Throne API、入站地址和端口、TUN、Kill Switch、NTP、日志以及文件路径。
- 请同时恢复 `profiles` 和 `routes`。如果只恢复其中之一，分组会失去自己的路由配置档。
- Throne 会拒绝含有会在其设备上运行程序或读取文件的配置档（例如额外核心配置档）的归档（`invalid_archive`）。
- 备份以明文保存配置档的凭据和 OTP 密钥。用完后请删除它们。

桌面版中的备份请参见[备份与恢复](@/guides/backup.zh.md#backup-restore)。

## 设置 {#settings}

```bash
thronectl settings
thronectl settings set kill_switch=true log_level=debug
```

`thronectl settings` 会列出 API 管理的设置及其当前值；[设置](@/advanced/api.zh.md#settings)中有它们的说明。其他设置只能在桌面版 Throne 中更改：请通过[恢复设置](#from-desktop)把它们迁移过来。

## 从其他设备管理 {#remote}

API 只监听 `127.0.0.1`。要从其他设备管理 Throne，请让它在网络上监听并设置允许列表。对于 systemd 单元，请把以下内容写入 `/etc/throne/throne.env`，然后运行 `sudo systemctl restart throne`：

```bash
THRONE_API_LISTEN=*:9095
THRONE_API_ALLOW=192.168.1.0/24
THRONE_API_ADMIN_LAN=1
```

在另一台设备上，把密钥放进一个只有你能读取的文件中，将 `THRONE_KEY_FILE` 设为该文件，并将 `THRONE_ADDR` 设为 `<box>:9095`。

- **管理请求**（导入、添加订阅、删除、更改设置、备份、读取日志、重启和关闭）只在来自 Throne 所在设备时才会得到响应，除非设置了 `THRONE_API_ADMIN_LAN=1`（`--api-admin-lan`）。否则它们会以 `forbidden` 失败。如果其他设备只需要监控和切换配置档，就不要设置它。
- 同一地址在一分钟内认证失败 5 次后，Throne 会在 60 秒内拒绝该地址。一个使用旧密钥的客户端可能会把同一 NAT 后面的其他客户端锁在外面。
- [Kill Switch](@/advanced/kill_switch.zh.md) 始终放行 API 的回复。在 Linux 上，TUN 可能会截走发给远程客户端的回复；参见 [Throne 如何保持可访问](@/headless/recovery.zh.md#protection)。
- 发往 API 的流量是加密并经过认证的。尽管如此，除非你需要在网络上使用 API，否则请让它保持在环回地址上，并且始终设置允许列表。

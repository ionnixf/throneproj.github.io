+++
title = "无界面 Throne"
description = "在服务器、路由器、树莓派或容器中以无界面（headless）方式运行 Throne，并使用 thronectl 控制它。"
weight = 4
sort_by = "weight"
toc = true
+++

`Throne --headless` 以无界面（headless）方式运行 Throne：可以运行在服务器、路由器、树莓派、NAS 或容器中。它与桌面版是同一个程序，拥有相同的核心、配置档、路由配置档和设置，但不显示窗口、托盘图标或对话框。你可以在终端中用 `thronectl` 通过 [Throne API](@/advanced/api.zh.md) 控制它；无界面 Throne 在首次启动时就会开启这个 API。

无界面 Throne 自 Throne 1.4.0 起在 Linux、macOS 和 Windows 上提供。Throne for Android 没有无界面模式。

典型用途：

- **代理设备。** Throne 运行一个配置档，网络中的其他设备使用它的混合（HTTP 和 SOCKS）入站。参见[与其他设备共享代理](@/guides/proxy_modes.zh.md#lan-sharing)。
- **网关。** Throne 在一台为整个网络路由流量的设备上以 TUN 模式运行。
- **容器。** Docker 镜像运行 Throne，并把数据保存在一个卷中。
- **你自己的电脑，不带窗口。** systemd 用户单元可以在你注销后让桌面版的配置档保持连接。

本部分的页面：

- [安装与服务](@/headless/installation.zh.md)：Linux 软件包、Docker、macOS 和 Windows 服务。
- [使用 thronectl 管理](@/headless/thronectl.zh.md)：连接 `thronectl`、添加订阅、把桌面版的配置迁移过来、更改设置。
- [Web 界面](@/headless/web.zh.md)：用 `thronectl web` 在浏览器中管理 Throne。
- [命令行与数据目录](@/headless/command_line.zh.md)：所有选项和变量、数据的存放位置、重启方式和退出码。
- [健康检查与日志](@/headless/monitoring.zh.md)：`Throne --check`、`Throne --health`、systemd 状态和日志。
- [防锁定与恢复](@/headless/recovery.zh.md)：Throne 如何让 API 始终可以访问，以及设置把你锁在外面时该怎么办。
- [故障排除](@/headless/troubleshooting.zh.md)：常见问题及其解决方法。

## 快速入门 {#quick-start}

在 Debian 10、Ubuntu 20.04 或更高版本（amd64）上：

1. 从[发布页面](https://github.com/throneproj/Throne/releases)下载 `Throne-<version>-debian-amd64.deb`。在 Debian 12、Ubuntu 22.04 或更高版本上，也可以改为添加 [APT 软件源](@/get_started/installation.zh.md#apt-repository)。
2. 安装软件包并启动服务：

   ```bash
   sudo apt install ./Throne-<version>-debian-amd64.deb
   sudo systemctl enable --now throne
   systemctl status throne
   ```

   `systemctl status` 会显示 Throne 正在做什么，例如 `Status: "Idle"`。

3. Throne 把数据保存在 `/var/lib/throne` 中，把 API 密钥保存在 `/var/lib/throne/config/api.key` 中，只有 root 能读取这个文件。将密钥复制到一个属于你自己的文件中：

   ```bash
   mkdir -p ~/.config/throne
   sudo install -m 600 -o "$USER" /var/lib/throne/config/api.key ~/.config/throne/api-key
   export THRONE_KEY_FILE=~/.config/throne/api-key
   ```

4. 软件包还会把命令行客户端 `thronectl` 安装为 `/usr/bin/thronectl`。如需从其他电脑管理 Throne，请参见[安装 thronectl](@/headless/thronectl.zh.md#install-thronectl)。
5. 添加你的订阅并连接：

   ```bash
   thronectl status
   thronectl sub add 'https://sub.example.com/abc' --name Provider
   thronectl profiles
   thronectl connect 12
   ```

   将 `12` 替换为 `thronectl profiles` 列表中某个配置档的 ID，或者使用 `thronectl connect best` 选择延迟最低的配置档。

现在 Throne 会运行该配置档，并在每次重启后再次启动它，直到你运行 `thronectl disconnect`。如果想在启动 Throne 之前检查这台设备究竟能否运行它，请运行 `sudo /opt/Throne/Throne --check`。参见 [Throne --check](@/headless/monitoring.zh.md#check)。

## 无界面 Throne 的工作方式 {#how-it-works}

Throne 从不自行转入后台。由服务管理器在开机时启动它、在出错后重新启动它，以及停止它：Linux 上是 systemd，macOS 上是 launchd，Windows 上是服务控制管理器（Service Control Manager），或者是 Docker。Throne 代码仓库为每一种都提供了现成的文件：

| 文件 | 说明 |
| --- | --- |
| [`dist/linux/throne.service`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne.service) | systemd 系统单元，以 root 身份运行。`.deb` 和 `.rpm` 软件包会将其安装为 `/usr/lib/systemd/system/throne.service`。 |
| [`dist/linux/throne.env`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne.env) | 该单元的环境文件，安装为 `/etc/throne/throne.env`。其中的每个变量都是被注释掉的示例。 |
| [`dist/linux/caps.conf`](https://github.com/throneproj/Throne/blob/dev/dist/linux/caps.conf) | 可选的 drop-in 文件：让该单元以用户 `throne` 的身份并借助能力（capabilities）运行，而不是以 root 身份运行。 |
| [`dist/linux/throne-user.service`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne-user.service) | systemd 用户单元，无需 root。 |
| [`dist/linux/throne-web.service`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne-web.service)、[`throne-web.env`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne-web.env) | [Web 界面](@/headless/web.zh.md#systemd)的 systemd 单元（以专用用户身份运行 `thronectl web`）及其环境文件。软件包会把它们安装为 `/usr/lib/systemd/system/throne-web.service` 和 `/etc/throne/web.env`。 |
| [`dist/macos/moe.Throne.headless.plist`](https://github.com/throneproj/Throne/blob/dev/dist/macos/moe.Throne.headless.plist) | launchd LaunchDaemon。 |
| [`dist/windows/install-service.bat`](https://github.com/throneproj/Throne/blob/dev/dist/windows/install-service.bat)、[`uninstall-service.bat`](https://github.com/throneproj/Throne/blob/dev/dist/windows/uninstall-service.bat) | 安装和移除 Windows 服务。 |
| [`docker/compose.yaml`](https://github.com/throneproj/Throne/blob/dev/docker/compose.yaml) | 用于镜像 `ghcr.io/throneproj/throne` 的 Docker Compose 示例。 |

无界面 Throne 运行时会发生什么：

- 它把配置档、分组、路由配置档和设置保存在[数据目录](@/headless/command_line.zh.md#data-dir)中，使用与桌面版相同的 `throne.db` 数据库。
- 从首次启动起，Throne API 就在 `127.0.0.1:9095` 上监听，使用的密钥由 Throne 生成并写入数据目录中的 `config/api.key`。`thronectl` 使用这个密钥。参见[将 thronectl 连接到 Throne](@/headless/thronectl.zh.md#connect)。
- 它会记住正在运行的配置档以及 TUN 是否开启，并在每次启动时再次启动它们，直到你断开连接。`--no-resume` 可以在单次运行中关闭这一行为。
- 它会向服务管理器报告自身状态，并监视自己的运行进度。当核心反复失败、某个配置档卡住或主循环停止时，它会以一个退出码退出，告诉服务管理器再次启动它。参见[退出码](@/headless/command_line.zh.md#exit-codes)。
- `Throne --health` 会询问正在运行的 Throne 状态如何，`Throne --check` 会告诉你这台电脑能否运行它。参见[健康检查与日志](@/headless/monitoring.zh.md)。

## 与桌面版的区别 {#differences}

无界面 Throne 不会询问任何问题。桌面版在对话框中询问的事项，无界面 Throne 会自行决定，或者写入日志：

| 情况 | 无界面 Throne |
| --- | --- |
| 通知或错误 | 写入日志。 |
| TUN 需要权限 | 以 `needs_privileges` 拒绝。`Throne --check` 会说明缺少什么。 |
| Xray 配置档需要 geoip 或 geosite 文件 | 下载这些文件，然后再尝试启动一次。 |
| [Kill Switch](@/advanced/kill_switch.zh.md)（断网保护）无法工作 | 在日志中记录原因，报告 `kill_switch_failed`，并拒绝启动配置档。 |
| VPN 配置档要求输入一次性验证码 | 如果该配置档有 [OTP 条目](@/advanced/vpn_profiles.zh.md#otp-manager)，就用它来回答。 |
| VPN 配置档要求提供其他内容，例如网页登录 | 在该询问过期时将其取消，最长 5 分钟（`vpn_challenge_pending`）。已保存的凭据被服务器拒绝时，结果为 `vpn_auth_failed`。 |
| 某项设置需要重启程序 | 记录到日志中。它会在下次重启（`thronectl restart`）后生效。 |
| WARP 服务条款 | 从不接受。 |

以下功能需要窗口，在无界面 Throne 中不可用：

- 系统代理（`thronectl proxy on` 会以 `system_proxy_unsupported` 失败）、托盘图标、全局热键和各种编辑器。
- 内置更新程序，以及以提升的权限或开启 TUN 的方式重新启动。请通过软件包、Docker 镜像或安装程序更新。
- `throne://` [深度链接](@/advanced/deeplinks.zh.md)和文件关联。请改用 `thronectl import` 导入。
- VPN 配置档的登录提示（例如 SSO）；来自 OTP 条目的一次性验证码除外。
- WARP 注册。
- API 不管理的设置，以及通过 API 恢复额外核心（Extra Core）配置档。

请在桌面版 Throne 中设置这些内容，然后用[备份](@/headless/thronectl.zh.md#from-desktop)迁移过来。对于 systemd 用户单元，请停止该单元并打开桌面版：两者使用相同的数据。

## 与桌面版 Throne 同时运行 {#beside-desktop}

TUN 的网卡（`throne-tun`）、核心的 DNS 端口（`dns_in_port`，默认为 5533）以及 TUN 的路由都属于整台电脑，因此同一时间只能有一个 Throne 在其上运行 TUN。只有满足以下条件时，无界面 Throne 才会开启 TUN：

- 不存在其他 `throne-tun` 网卡（在 Linux 和 Windows 上检查）；
- 没有网卡已经占用 TUN 地址（`tun_ipv4_cidr`，默认为 `172.19.0.1/24`）；并且
- 在没有配置档运行时，DNS 端口是空闲的。

否则 `thronectl tun on` 会以 `tun_conflict` 失败，`thronectl status` 会显示是什么占用了 TUN。如果启动时无法恢复 TUN，`Throne --health` 会报告 `tun_unavailable`。如果 Docker 网桥或其他网络使用了 Throne 的 TUN 地址范围，请将 TUN 移到一个空闲的范围，然后重新开启它：

```bash
thronectl settings set tun_ipv4_cidr=172.29.0.1/24
thronectl tun on
```

如果另一个程序占用了 DNS 端口，请用 `thronectl settings set dns_in_port=PORT` 为 Throne 指定一个空闲端口。

无界面 Throne 还会留下正在运行的标记：以 root 身份运行时位于 `/run/throne`（macOS 上为 `/var/run/throne`），以普通用户身份运行时位于 `$XDG_RUNTIME_DIR/throne`，在 Windows 上则为 `Global\ThroneHeadless`（既未提升权限、也不是服务时为 `Local\ThroneHeadless`，只在同一个 Windows 会话中可见）。此时，同一台电脑上的桌面版 Throne 会：

- 在启动时警告有无界面 Throne 正在运行；
- 在无界面 Throne 运行期间拒绝开启 TUN；并且
- 在从同一安装位置运行的无界面 Throne 运行期间，不会自行更新。

两个 Throne 永远不会共用同一个数据目录。如果无界面用户单元使用了桌面版的数据目录，桌面版会提示 Throne 正在后台运行，然后退出。请先用 `systemctl --user stop throne` 停止该单元。拥有自己数据目录的第二个无界面 Throne 可以与第一个同时运行；上述检查会阻止两者同时运行 TUN。

## 设备 ID {#device-id}

有些订阅要求提供设备 ID（即 `x-hwid` 请求头，参见 [User-Agent 与 HWID](@/guides/subscriptions.zh.md#user-agent-and-hwid)）。在 Linux 上，Throne 从 `/etc/machine-id` 读取它，如果该文件不存在，则从 `/var/lib/dbus/machine-id` 读取。两个文件都没有的系统（例如容器）会在首次启动时在 `config/hwid` 中获得一个生成的 ID。该 ID 跟随数据目录。

官方 Docker 镜像不包含 machine ID，因此每个容器都会获得自己的 ID。如果你自己构建镜像，请不要在其中放入 `machine-id` 文件：否则由该镜像创建的每个容器都会发送相同的 ID。

macOS 使用硬件 UUID，Windows 使用注册表中的 `MachineGuid`。

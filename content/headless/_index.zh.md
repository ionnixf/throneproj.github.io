+++
title = "无界面 Throne"
description = "在服务器、路由器、树莓派或容器中以无界面（headless）方式运行 Throne，并使用 thronectl 控制它。"
weight = 4
sort_by = "weight"
toc = true
+++

`Throne --headless` 以无界面（headless）方式运行 Throne：可以运行在服务器、路由器、树莓派或容器中。它与桌面版是同一个程序，拥有相同的核心、配置档和设置。你通过 [Throne API](@/advanced/api.zh.md) 用 `thronectl` 控制它，这个 API 从首次启动起就是开启的。它需要 Linux、macOS 或 Windows 上的 Throne 1.4.0 或更高版本。Throne for Android 没有无界面模式。

典型用途：一台代理设备，[其他设备使用](@/guides/proxy_modes.zh.md#lan-sharing)它的混合入站；为整个网络提供 TUN 的网关；Docker 容器；或者一个 [systemd 用户单元](@/headless/installation.zh.md#user-unit)，在你注销后让桌面版的配置档保持连接。

## 快速入门 {#quick-start}

在 Debian 10、Ubuntu 20.04 或更高版本（amd64）上：

1. 从[发布页面](https://github.com/throneproj/Throne/releases)下载 `Throne-<version>-debian-amd64.deb`，或者添加 [APT 软件源](@/get_started/installation.zh.md#apt-repository)（Debian 12、Ubuntu 22.04 或更高版本）。
2. 安装软件包并启动服务：

   ```bash
   sudo apt install ./Throne-<version>-debian-amd64.deb
   sudo systemctl enable --now throne
   ```

3. 只有 root 能读取 API 密钥。将它复制到一个属于你自己的文件中：

   ```bash
   mkdir -p ~/.config/throne
   sudo install -m 600 -o "$USER" /var/lib/throne/config/api.key ~/.config/throne/api-key
   export THRONE_KEY_FILE=~/.config/throne/api-key
   ```

4. 用软件包安装的 `thronectl` 添加你的订阅并连接：

   ```bash
   thronectl status
   thronectl sub add 'https://sub.example.com/abc' --name Provider
   thronectl profiles
   thronectl connect 12
   ```

   将 `12` 替换为列表中某个配置档的 ID，或者使用 `thronectl connect best` 选择延迟最低的配置档。

Throne 会在每次重启后再次启动这个配置档，直到你运行 `thronectl disconnect`。如果有问题，请运行 `sudo /opt/Throne/Throne --check`（参见 [Throne --check](@/headless/monitoring.zh.md#check)）。Docker、macOS、Windows 以及其他 Linux 安装方式请参见[安装与服务](@/headless/installation.zh.md)。

## 无界面 Throne 的工作方式 {#how-it-works}

- 由服务管理器启动、重启和停止它：systemd、launchd、Windows 服务控制管理器或 Docker。Throne 从不自行转入后台。
- 它把配置档、分组、路由配置档和设置保存在[数据目录](@/headless/command_line.zh.md#data-dir)中，使用与桌面版相同的 `throne.db`。
- API 在 `127.0.0.1:9095` 上监听，使用 Throne 在数据目录中的 `config/api.key` 里生成的密钥。参见[将 thronectl 连接到 Throne](@/headless/thronectl.zh.md#connect)。
- 每次启动时，它都会再次启动上一个配置档；如果之前开启了 TUN，也会一并开启。`--no-resume` 可以跳过一次。
- 当核心反复失败或某个配置档卡住时，它会以一个退出码退出，让服务管理器再次启动它。参见[退出码](@/headless/command_line.zh.md#exit-codes)。

## 与桌面版的区别 {#differences}

无界面 Throne 不会询问任何问题。它会自行决定，或者写入日志：

- 在没有所需权限时开启 TUN 会以 `needs_privileges` 失败。`Throne --check` 会说明缺少什么。
- 如果 [Kill Switch](@/advanced/kill_switch.zh.md)（断网保护）无法工作，Throne 会报告 `kill_switch_failed`，并拒绝启动配置档。
- VPN 配置档要求输入一次性验证码时，会从它的 [OTP 条目](@/advanced/vpn_profiles.zh.md#otp-manager)获取。其他询问（例如网页登录）会在过期时被取消，最长 5 分钟（`vpn_challenge_pending`）。已保存的凭据被拒绝时，结果为 `vpn_auth_failed`。
- 需要重启程序的设置会在 `thronectl restart` 后生效。

没有系统代理（`thronectl proxy on` 会以 `system_proxy_unsupported` 失败）、托盘图标、全局热键和内置更新程序：请通过软件包、Docker 镜像或安装程序更新。`throne://` [深度链接](@/advanced/deeplinks.zh.md)和文件关联不可用；请改用 `thronectl import`。

OTP 条目、VPN 凭据以及 API 不管理的设置，请在桌面版 Throne 中设置，然后用[备份](@/headless/thronectl.zh.md#from-desktop)迁移过来。systemd 用户单元与桌面版共用数据：停止该单元，再打开桌面版即可。

## 与桌面版 Throne 同时运行 {#beside-desktop}

同一时间只能有一个 Throne 在一台电脑上运行 TUN，因为 `throne-tun` 网卡、TUN 的地址范围（`tun_ipv4_cidr`，默认为 `172.19.0.1/24`）和核心的 DNS 端口（`dns_in_port`，默认为 5533）都属于整台电脑。其中任何一项被占用时，`thronectl tun on` 会以 `tun_conflict` 失败，`thronectl status` 会显示是什么占用了它。如果 Docker 网桥或其他网络使用了 TUN 的地址范围，请将 TUN 移到空闲的范围；如果另一个程序占用了 DNS 端口，请为 Throne 指定另一个端口：

```bash
thronectl settings set tun_ipv4_cidr=172.29.0.1/24    # 或：dns_in_port=PORT
thronectl tun on
```

无界面 Throne 运行期间，同一台电脑上的桌面版 Throne 会在启动时发出警告、拒绝开启 TUN；如果两者从同一安装位置运行，它也不会自行更新。

两个 Throne 永远不会共用同一个数据目录。如果用户单元使用了桌面版的数据目录，桌面版会提示 Throne 正在后台运行，然后退出。请先停止该单元：`systemctl --user stop throne`。

## 设备 ID {#device-id}

有些订阅要求提供设备 ID（参见 [User-Agent 与 HWID](@/guides/subscriptions.zh.md#user-agent-and-hwid)）。在 Linux 上，Throne 从 `/etc/machine-id` 读取它，如果该文件不存在，则从 `/var/lib/dbus/machine-id` 读取。两个文件都没有时（例如在容器中），它会在首次启动时在 `config/hwid` 中生成一个 ID，该 ID 跟随数据目录。macOS 使用硬件 UUID，Windows 使用注册表中的 `MachineGuid`。

官方 Docker 镜像不包含 machine ID，因此每个容器都会获得自己的 ID。如果你自己构建镜像，请不要在其中放入 `machine-id` 文件，否则由该镜像创建的每个容器都会发送相同的 ID。

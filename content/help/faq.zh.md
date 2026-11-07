+++
title = "常见问题"
description = "关于 Throne 桌面版常见问题的简短回答。"
weight = 10
toc = true
+++

如果遇到故障，请参阅[故障排除](@/help/troubleshooting.zh.md)。

## Throne 与 Nekoray 有什么不同？ {#nekoray}

Throne 是 Nekoray 的延续，Nekoray 已不再开发。Throne 运行在自己的基于 sing-box 的核心 ThroneCore 上，只有 Xray 配置档才会用到 Xray，并新增了自动选择器、Cloudflare WARP、OpenVPN 和 OpenConnect 配置档、备份以及 Android 应用等功能。Throne 无法读取 Nekoray 的配置；请参阅[从 Nekoray / NekoBox 迁移](@/help/migrating.zh.md)。

## 找不到文档中提到的菜单项或设置，它在哪里？ {#missing-option}

全新安装会以简洁模式启动，该模式会隐藏高级项。按 `Ctrl+K` 并输入设置的名称即可找到并打开它，也可以在 `设置`（Settings）→ `基本设置`（Basic Settings）→ `样式`（Style）→ `界面`（Interface）中切换到高级模式。如果仍然找不到，可能是你的版本比本文档描述的版本旧，或者该项只存在于其他系统上。参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)。

## 有便携模式吗？ {#portable}

有。Windows 和 Linux 的 ZIP 版本会将所有数据保存在程序旁边的 `config` 文件夹中，因此你可以把整个 `Throne` 文件夹复制到其他位置或其他电脑。软件包、Linux 安装脚本以及 macOS 应用会将数据保存在你的用户文件夹中。参见[命令行、文件与日志](@/reference/files.zh.md#data-folder)。

## 我可以在 Android 上使用 Throne 并把设置迁移过去吗？ {#android}

可以。[Throne for Android](@/android/_index.zh.md) 使用相同的核心和相同的备份格式。在 `设置` → `基本设置`（Basic Settings）→ `备份和恢复`（Backup and Restore）中创建备份，将 `.thrbackup` 文件复制到手机，然后在应用中恢复；反过来也可以。参见[备份、更新与迁移](@/guides/backup.zh.md#desktop-android)。

## Throne 能在没有窗口的服务器上运行吗？ {#headless}

可以。`Throne --headless` 可以运行在服务器、路由器或容器中；Linux 软件包包含用于它的 systemd 服务，另外还有 Docker 镜像。你可以用 `thronectl` 或它的 [Web 界面](@/headless/web.zh.md)控制它。参见[无界面 Throne](@/headless/_index.zh.md)。

## 我可以用脚本或 Stream Deck 控制 Throne 吗？ {#scripts}

可以。开启 `设置`（Settings）→ `Integration Settings` → `Throne API`（仅在高级模式下可用），然后使用 `thronectl`：例如，把 `thronectl toggle` 设在 Stream Deck 的一个按键上即可连接和断开。不使用 API 时，[全局热键](@/reference/shortcuts.zh.md#global-hotkeys) `Start/Stop Profile` 和 `Toggle Tun Mode` 也可以在任何应用中使用。参见 [Throne API 与 thronectl](@/advanced/api.zh.md)。

## 为什么杀毒软件会将 Throne 标记为威胁？ {#antivirus}

这些都是由正常功能引起的误报：更新程序会替换程序文件，Throne 会在 Windows 上写入注册表项，TUN 模式会更改网络设置（[#1127](https://github.com/throneproj/Throne/issues/1127)）。请只从官方[下载页面](@/downloads.zh.md)下载 Throne，并将 Throne 文件夹添加为例外。杀毒软件的网络防护也可能在没有任何提示的情况下阻止 TUN 模式；参见[故障排除](@/help/troubleshooting.zh.md#tun)。

## 我的设备 ID（HWID）会被发送给别人吗？ {#hwid}

只有在你开启时才会。`设置` → `基本设置` → `订阅`（Subscription）→ `启用在更新订阅时发送硬件ID、设备型号以及 OS 版本`（Enable sending HWID, device model, and OS version when updating subscription）默认关闭；开启后，这些值只会随订阅请求发送。每个分组都可以在其 `高级`（Advanced）订阅设置中通过 `发送硬件标识`（Send HWID）覆盖此设置（两者都仅在高级模式下可用）。有些服务商要求发送这些信息。参见[隐私与网络请求](@/reference/privacy.zh.md#hwid)。

## 有断网保护（kill switch）吗？ {#kill-switch}

有：`设置` → `基本设置` → `安全`（Security）→ `Kill Switch`。Throne 运行期间，它会拦截所有不经过 Throne 的流量，即使没有配置档运行时也是如此；它需要与 TUN 模式相同的权限。参见 [Kill Switch（断网保护）](@/advanced/kill_switch.zh.md)。在 Android 上，请将 Throne 设为始终开启的 VPN，并开启系统中阻止未使用 VPN 的连接的选项；参见[权限与后台运行](@/android/permissions.zh.md#always-on)。

## 为什么 TUN 模式需要管理员或 root 权限？ {#tun-admin}

TUN 模式会创建一个捕获系统流量的虚拟网卡，而操作系统只允许管理员这样做。系统代理模式不需要特殊权限。在 Windows 上，Throne 会以管理员身份重新启动，此后每次都以管理员身份启动，除非你开启 `设置` → `基本设置` → `安全`（Security）→ `始终以标准用户身份启动`（Always Start as Standard User）（仅在高级模式下可用）。在 Linux 和 macOS 上，只有核心会获得 root 权限，窗口不会。参见 [TUN 模式](@/guides/tun_mode.zh.md#privileges)。

## Linux 上真的需要 SUID 位吗？ {#linux-suid}

TUN 模式需要核心具有 root 权限，而 Throne 窗口以你的普通用户身份运行，所以不要用 `sudo` 启动 Throne。默认情况下，Throne 会使用 `pkexec`（polkit）将 `ThroneCore` 的所有者改为 root，并设置其 SUID 位。你也可以改用 `setcap` 为核心授予五项能力（capabilities）。要让 Throne 不再请求权限，请开启 `设置` → `基本设置` → `安全` → `禁止权限请求`（Disable Privilege request）（仅在高级模式下可用）。参见 [TUN 模式](@/guides/tun_mode.zh.md#privileges)。

## 为什么 Throne 被强制关闭后无法上网？ {#force-quit}

开启 `系统代理`（System Proxy）后，Throne 会在配置档运行期间将系统的代理设置指向它的本地端口，并在配置档停止或 Throne 退出时移除该设置。如果 Throne 被强行结束，该设置会保留下来，应用会尝试使用一个已不再运行的代理。解决方法：启动 Throne，在勾选 `系统代理` 的情况下启动任意配置档，然后停止它，或通过 `程序`（Program）→ `退出`（Exit）退出。你也可以在系统的网络设置中关闭代理。

## 为什么配置档停止时系统代理会关闭？ {#system-proxy-off}

Throne 只在配置档运行期间设置系统代理，这样应用就不会尝试使用未运行的代理。`系统代理`（System Proxy）复选框会保持勾选，当你启动配置档时，Throne 会再次设置代理。参见[系统代理、TUN 与局域网共享](@/guides/proxy_modes.zh.md#system-proxy)。

## 为什么 DNS 泄漏测试显示的是我的运营商（ISP）的 DNS 服务器？ {#dns-leak-test}

这通常是正常的。匹配 `direct` 规则的域名会使用 `直连 DNS`（Direct DNS）解析，它通常就是你的运营商的 DNS；在系统代理模式下，应用和浏览器也可能不经过 Throne 自行解析域名。[DNS 泄漏测试](@/guides/dns.zh.md#leak-tests)列出了预期的结果以及需要修改的内容。

## 为什么订阅更新后我的配置档停止了，或者仍在运行？ {#profile-after-update}

只有在你允许时，更新才会停止正在运行的配置档。如果更新会删除正在运行的配置档，Throne 会保留它，除非开启了 `设置` → `基本设置` → `订阅` → `允许停止活动配置档`（Allow stopping the active profile）（仅在高级模式下可用）。如果服务商只修改了它的设置，Throne 会保存这些设置但不会重新连接；请重新启动该配置档以使用新设置。参见[订阅与分组](@/guides/subscriptions.zh.md#running-profile)。

## 为什么我的订阅中出现了 `Custom` 配置档？ {#custom-config-import}

Throne 会将 sing-box 和 Xray 的 JSON 拆分为每个服务器一个配置档，但会将完整的 Xray 配置（`Custom Xray Config`）、VLESS 以外协议的 Xray 出站（`Custom Xray … Outbound`）以及单个 sing-box 出站（`Custom … Outbound`）原样保留。`selector` 和 `urltest` 等分组类出站不会被导入；请改用[自动选择器](@/guides/testing.zh.md#auto-selector)。参见[协议与导入格式](@/reference/protocols.zh.md#import-formats)。

## 可下载的路由配置档来自哪里？ {#route-profiles-source}

它们来自 [throneproj/routeprofiles](https://github.com/throneproj/routeprofiles) 仓库，内置规则集 `geoip-…` 和 `geosite-…` 的列表也保存在那里。规则集文件本身来自 MetaCubeX meta-rules-dat、Chocolate4U Iran-sing-box-rules 和 runetfreedom russia-v2ray-rules-dat 等项目。参见[路由](@/guides/routing.zh.md#download-profiles)。

## `.deb` 软件包之间有什么区别？ {#deb-variants}

普通的 `.deb` 自带一份 Qt，可在 Debian 10、Ubuntu 20.04 或更高版本上运行。`-system-qt.deb` 使用你的发行版提供的 Qt 6，需要 Debian 12、Ubuntu 22.04 或更高版本。请先使用普通软件包；如果它无法启动，例如在不支持 SSE4.2 的处理器上（[#845](https://github.com/throneproj/Throne/issues/845)），请使用 `system-qt`。arm64 和 `.rpm` 也有同样的一对软件包；参见[安装](@/get_started/installation.zh.md#linux)。

## 哪些软件包是官方的？ {#third-party-packages}

官方的有：[GitHub 发布页面](https://github.com/throneproj/Throne/releases)上的文件、Linux 安装脚本、APT 和 RPM 软件源、AUR 中的 `throne` 软件包以及 Homebrew cask `throneproj/tap/throne`。WinGet、Scoop 和 Nix 软件包，以及 `throne-bin` 等其他 AUR 软件包，由社区制作，开发者不为其提供支持（[#1182](https://github.com/throneproj/Throne/issues/1182)、[#1622](https://github.com/throneproj/Throne/issues/1622)）。如果问题只在这类软件包中出现，请将其报告给软件包的维护者。参见[包管理器](@/get_started/installation.zh.md#package-managers)。

## Windows 7、旧版 macOS、旧版 Linux 或旧处理器需要哪个版本？ {#old-systems}

- **Windows 7 SP1 和 Windows 8：** `windowslegacy64.zip`（64 位）、`windows32.zip`（32 位），或会自动选择 legacy 版本的通用安装程序。
- **运行 macOS 10.15 至 14.3 的 Intel Mac：** `macoslegacy-amd64.zip`。更旧的 macOS 版本不受支持。
- **旧版 Linux 发行版**（Debian 10、Ubuntu 20.04、RHEL 8）：ZIP、安装脚本或普通软件包。`-system-qt` 软件包和软件源需要 Debian 12、Ubuntu 22.04、RHEL 9 或更高版本。
- **不支持 SSE4.2 的处理器：** Windows 上使用 `windowslegacy64.zip`，Linux 上使用 `-system-qt` 软件包（[#845](https://github.com/throneproj/Throne/issues/845)）。

[下载](@/downloads.zh.md)页面的表格列出了每个文件的最低系统要求。

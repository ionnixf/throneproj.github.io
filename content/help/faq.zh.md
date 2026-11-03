+++
title = "常见问题"
description = "关于 Throne 的常见问题简答：Nekoray、杀毒软件报警、断网保护、无界面服务器、脚本、TUN 权限、软件包和旧系统。"
weight = 10
toc = true
+++

这里简要回答关于 Throne 桌面版的常见问题。每个回答都链接到包含详细信息的页面。如果遇到故障，请前往[故障排除](@/help/troubleshooting.zh.md)。

## Throne 与 Nekoray 有什么不同？ {#nekoray}

Throne 是 Nekoray 的延续。Nekoray 已不再开发，其仓库已归档。Throne 以 Nekoray 的代码为起点，此后有了很大变化：

- 核心是基于 sing-box 的 ThroneCore。只有 Xray 配置档（例如 `VLESS (Xray)`）才会在核心内部运行 Xray。
- 新增了许多功能，例如自动选择器、Cloudflare WARP、OpenVPN 和 OpenConnect 配置档、备份与恢复，以及 Android 应用。
- 不再有用的旧功能已被移除或简化。

Throne 无法读取 Nekoray 的配置。要迁移你的服务器和设置，请参阅[从 Nekoray / NekoBox 迁移](@/help/migrating.zh.md)。

## 找不到文档中提到的菜单项或设置，它在哪里？ {#missing-option}

自 1.4.0 起，全新安装的 Throne 会以简洁模式启动，该模式会隐藏高级菜单项和设置。被隐藏的设置会保留原值并继续生效。

- 要显示全部内容，请打开 `设置`（Settings）→ `基本设置`（Basic Settings）→ `样式`（Style），将 `界面`（Interface）设为 `高级`（Advanced），然后点击 `确定`（OK）。
- 要查找某项设置，请在主窗口中按 `Ctrl+K` 并输入它的名称。搜索结果也会列出被隐藏的设置；选择其中一项后，Throne 会切换到高级模式并打开该设置。
- 在简洁模式下，有些标签页会成为其他标签页中的区域。例如，`基本设置` 中 `订阅`（Subscription）和 `安全`（Security）的选项位于 `通用`（Common）标签页的底部。

如果仍然找不到，可能是你的版本比本文档描述的版本旧，或者该项只存在于其他系统上。参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)。

## 有便携模式吗？ {#portable}

有。Windows 和 Linux 的 ZIP 版本是便携版：Throne 会将所有数据保存在程序旁边的 `config` 文件夹中。要将 Throne 移动到其他文件夹或其他电脑，请复制整个 `Throne` 文件夹。通过 Windows 安装程序进行的按用户安装（默认方式）也会将 `config` 保存在程序旁边。

`.deb` 和 `.rpm` 软件包、Linux 安装脚本以及 macOS 应用则会将数据保存在你的用户文件夹中。当程序文件夹不可写时（例如为所有用户安装到 `Program Files`），Throne 也会使用你的用户文件夹，并将已有数据复制过去。`设置`（Settings）→ `打开配置文件夹`（Open Config Folder）会打开当前使用的文件夹（仅在高级模式下可用）。参见[命令行、文件与日志](@/reference/files.zh.md#data-folder)。

## 我可以在 Android 上使用 Throne 并把设置迁移过去吗？ {#android}

可以。[Throne for Android](@/android/_index.zh.md) 2.0.0（前身为 NekoBox for Android）与桌面版使用相同的核心和相同的备份格式。在桌面版的 `设置` → `基本设置`（Basic Settings）→ `备份和恢复`（Backup and Restore）中创建备份，将 `.thrbackup` 文件复制到手机，然后在 Android 应用中恢复。反过来从 Android 迁移到桌面版也可以。参见[备份、更新与迁移](@/guides/backup.zh.md#desktop-android)。

## Throne 能在没有窗口的服务器上运行吗？ {#headless}

可以，自 1.4.0 起。`Throne --headless` 以无窗口方式运行同一个程序，没有窗口、托盘图标或对话框：可以运行在服务器、路由器、树莓派或容器中。`.deb` 和 `.rpm` 软件包包含一个用于它的 systemd 服务（软件包从不自行启用它），另外还有 Docker 镜像。你可以用 `thronectl` 通过 Throne API 控制无界面 Throne，也可以在浏览器中通过 `thronectl web` 提供的 [Web 界面](@/headless/web.zh.md)控制它。软件包和 Docker 镜像都包含 `thronectl`。参见[无界面 Throne](@/headless/_index.zh.md)。

## 我可以用脚本或 Stream Deck 控制 Throne 吗？ {#scripts}

可以，自 1.4.0 起。在 `设置`（Settings）→ `Integration Settings` → `Throne API` 中开启 Throne API（仅在高级模式下可用），然后使用 `thronectl`。例如，`thronectl toggle` 可以用 Stream Deck 或宏键盘上的一个按键连接和断开，`thronectl connect best` 会启动延迟最低的配置档。参见 [Throne API 与 thronectl](@/advanced/api.zh.md)。

不使用 API 时，[全局热键](@/reference/shortcuts.zh.md#global-hotkeys) `Start/Stop Profile` 和 `Toggle Tun Mode` 也可以在任何应用中使用。

## 为什么杀毒软件会将 Throne 标记为威胁？ {#antivirus}

这些都是误报。一些杀毒软件会对 Throne 为实现正常功能所做的操作产生反应：

- 内置更新程序会下载新版本并替换程序文件。恶意软件也会替换文件，所以这看起来很可疑。
- 在 Windows 上，Throne 会写入注册表项：`HKEY_CURRENT_USER\Software\Classes` 下的 `throne://` 链接注册、安装程序记录的安装文件夹，以及在 Throne 以管理员身份运行时写入的 Windows 错误报告设置（用于将崩溃转储保存到 Throne 的 `crashes` 文件夹）。这些注册表项并不是后门（[#1127](https://github.com/throneproj/Throne/issues/1127)）。
- 在 TUN 模式下，核心会创建虚拟网卡并更改网络设置。

请只从官方[下载页面](@/downloads.zh.md)下载 Throne，并在杀毒软件中将 Throne 文件夹添加为例外。杀毒软件的网络防护也可能在没有任何提示的情况下阻止 TUN 模式；参见[故障排除](@/help/troubleshooting.zh.md#tun)。

## 我的设备 ID（HWID）会被发送给别人吗？ {#hwid}

只有在你开启时才会。`设置` → `基本设置` → `订阅`（Subscription）→ `启用在更新订阅时发送硬件ID、设备型号以及 OS 版本`（Enable sending HWID, device model, and OS version when updating subscription）默认关闭。开启后，Throne 会将这些值作为 HTTP 请求头添加到订阅请求中，而不会添加到其他任何请求中。自 1.3.1 起，每个分组都可以在其 `高级`（Advanced）订阅设置中通过 `发送硬件标识`（Send HWID）覆盖此设置。这两项设置都只在高级模式下显示。有些服务商要求发送这些信息。参见[隐私与网络请求](@/reference/privacy.zh.md#hwid)。

## 有断网保护（kill switch）吗？ {#kill-switch}

有，自 1.4.0 起作为实验性功能提供。请开启 `设置` → `基本设置` → `安全`（Security）→ `Kill Switch`。在简洁模式下，`安全` 中的选项位于 `通用`（Common）标签页的底部。Throne 运行期间，它会拦截所有不经过 Throne 的流量，即使没有配置档运行时也是如此。除非在 `Tun 设置`（Tun Settings）中关闭了 `私有地址范围绕过`（Private Range Bypass），否则本地网络仍可访问。断网保护需要与 TUN 模式相同的权限：在 Windows 上需要管理员权限，在 Linux 和 macOS 上需要具有 root 权限的核心。参见 [Kill Switch（断网保护）](@/advanced/kill_switch.zh.md)。

Throne 1.3 及更早版本没有断网保护。当配置档停止，或者核心意外停止时，你的应用会不经代理直接连接。

**Android：** Android 系统本身可以在 VPN 未连接时阻止所有流量。将 Throne 设为始终开启的 VPN，并开启系统中阻止未使用 VPN 的连接的选项。参见[权限与后台运行](@/android/permissions.zh.md#always-on)。

## 为什么 TUN 模式需要管理员或 root 权限？ {#tun-admin}

TUN 模式会创建一个虚拟网卡，并将系统的流量导入其中。操作系统只允许管理员这样做。系统代理模式不需要特殊权限。

- **Windows：** Throne 会提示“请以管理员身份运行 Throne”（Please run Throne as admin），并以管理员权限重新启动自身。此后，它每次都会以管理员身份启动。要停止这种行为，请开启 `设置` → `基本设置` → `安全`（Security）→ `始终以标准用户身份启动`（Always Start as Standard User）（仅在高级模式下可用）。
- **Linux：** 只有核心（`ThroneCore`）会获得 root 权限，Throne 窗口不会。参见[下一个问题](#linux-suid)。
- **macOS：** Throne 会打开“终端”（Terminal）并运行 `sudo`，为核心授予 root 权限。这需要管理员账户。

参见 [TUN 模式](@/guides/tun_mode.zh.md#privileges)。

## Linux 上真的需要 SUID 位吗？ {#linux-suid}

TUN 模式需要具有 root 权限的核心，但 Throne 窗口必须以你的普通用户身份运行。不要用 `sudo` 启动 Throne 本身。

当你开启 `Tun 模式`（Tun Mode）时，Throne 会提示“请赋予核心 root 权限”（Please give the core root privileges）。如果你同意，它会使用 `pkexec` 将 `ThroneCore` 的所有者改为 root，并设置其 SUID 位。系统中必须安装 `pkexec`（polkit）。

除了 SUID 位，你也可以为核心授予五项能力（capabilities）。只有当核心拥有全部五项能力时，Throne 才会将其视为已获得权限：

```bash
sudo setcap cap_net_admin,cap_net_raw,cap_net_bind_service,cap_sys_ptrace,cap_dac_read_search+ep /opt/Throne/ThroneCore
```

此路径适用于 `.deb` 和 `.rpm` 软件包以及 Linux 安装脚本。对于 ZIP 版，请使用你的 Throne 文件夹中的 `ThroneCore` 文件。

要让 Throne 不再请求权限，请开启 `设置` → `基本设置` → `安全` → `禁止权限请求`（Disable Privilege request）（仅在高级模式下可用）。此后，TUN 模式和其他需要 root 的功能只有在你自行授予权限后才能工作。某些发行版的软件包无法修改核心；此时 Throne 会显示“This installation cannot grant the core privileges by itself.”（此安装无法自行为核心授予权限），并附上打包者提供的说明。

## 为什么 Throne 被强制关闭后无法上网？ {#force-quit}

开启 `系统代理`（System Proxy）后，Throne 会在配置档运行期间将系统的代理设置指向它的本地端口（默认为 `127.0.0.1:2080`）。当配置档停止或你退出 Throne 时，它会移除该设置。如果 Throne 被强行结束（例如在任务管理器中被结束，或因断电），该设置会保留下来，应用会尝试使用一个已不再运行的代理。

解决方法：

1. 启动 Throne。
2. 如果 `系统代理` 未勾选，请勾选它，然后启动任意配置档。
3. 停止该配置档，或通过 `程序`（Program）→ `退出`（Exit）退出 Throne。

Throne 会在第 3 步移除代理设置。你也可以在系统的网络设置中关闭代理。

## 为什么配置档停止时系统代理会关闭？ {#system-proxy-off}

自 1.1.3 起，Throne 只在配置档运行期间设置系统代理。配置档停止时，Throne 会移除该设置，这样应用就不会尝试使用未运行的代理。`系统代理` 复选框会保持勾选，当你启动配置档时，Throne 会再次设置系统代理。参见[系统代理、TUN 与局域网共享](@/guides/proxy_modes.zh.md#system-proxy)。

## 为什么 DNS 泄漏测试显示的是我的运营商（ISP）的 DNS 服务器？ {#dns-leak-test}

这通常是正常的。例如，匹配 `direct` 规则的域名会使用 `直连 DNS`（Direct DNS）解析，而它默认是系统的 DNS，通常就是你的运营商的 DNS。在系统代理模式下，应用也可能自行解析域名，浏览器也可能使用自己的 DNS 设置，因此这些查询根本不会到达 Throne。[DNS 泄漏测试](@/guides/dns.zh.md#leak-tests)列出了预期的结果，并说明了结果不符合预期时应该修改什么。

## 为什么订阅更新后我的配置档停止了，或者仍在运行？ {#profile-after-update}

除非你允许，否则订阅更新不会停止正在运行的配置档：

- 如果新列表中不再包含正在运行的配置档，或者某个清理选项会删除它，Throne 会将该配置档保留在分组中，并让它继续运行。变更报告会将其列在“仍在使用中，因此保留而不是删除”（Still in use, so kept instead of deleted）之下，或者显示“The running profile was kept.”（正在运行的配置档已保留）。
- 如果你开启了 `设置` → `基本设置` → `订阅` → `允许停止活动配置档`（Allow stopping the active profile）（仅在高级模式下可用），在这种情况下 Throne 会停止并删除正在运行的配置档。
- 如果服务商只修改了正在运行的配置档的部分设置，Throne 会更新已保存的配置档，但不会重新建立连接。请重新启动该配置档以使用新设置。

如果某个自动选择器使用了该分组，当更新替换了它正在使用的配置档时，它会自行重建。参见[订阅与分组](@/guides/subscriptions.zh.md#running-profile)。

## 为什么我的订阅中出现了 `Custom` 配置档？ {#custom-config-import}

Throne 会将 sing-box 和 Xray 的 JSON 拆分为每个服务器一个配置档。少数内容会被有意导入为自定义配置档。`类型`（Type）列会将它们显示为：

- `Custom Xray Config`：如果订阅是一组完整的 Xray 配置，且每个配置都有自己的 `outbounds`，则每个配置都会生成一个这样的配置档。这些配置可能包含必须原样运行的负载均衡器或代理链。
- `Custom Xray … Outbound`：VLESS 以外协议的 Xray 出站。
- `Custom … Outbound`：单个 sing-box 出站，即一个带有 `type` 字段的 JSON 对象。

`selector` 和 `urltest` 等分组类出站不会被导入。要自动选择最佳服务器，请使用[自动选择器](@/guides/testing.zh.md#auto-selector)。

Throne 1.1.0 和 1.1.1 会将带有 `inbounds` 的完整 sing-box 配置作为一个自定义配置导入。自 1.1.2 起，这类配置会再次被拆分为多个配置档。参见[协议与导入格式](@/reference/protocols.zh.md#import-formats)。

## 可下载的路由配置档来自哪里？ {#route-profiles-source}

来自 [throneproj/routeprofiles](https://github.com/throneproj/routeprofiles) 仓库。`路由`（Routing）→ `下载配置档`（Download Profiles）会从该仓库获取各个国家的配置档。内置规则集（`geoip-…` 和 `geosite-…`）的列表也保存在那里；规则集文件本身来自 MetaCubeX meta-rules-dat、Chocolate4U Iran-sing-box-rules 和 runetfreedom russia-v2ray-rules-dat 等项目。Throne 通过 `路由设置`（Routing Settings）→ `通用`（Common）→ `远程规则集镜像`（Remote Rule-set Mirror）中选择的镜像下载它们（该设置仅在高级模式下可用）。参见[路由](@/guides/routing.zh.md#download-profiles)。

## `.deb` 软件包之间有什么区别？ {#deb-variants}

- `Throne-<version>-debian-amd64.deb` 自带一份 Qt 库。它的体积较大，但不依赖系统的 Qt 版本。自 1.4.0 起，它也能在更旧的系统上运行，最低可到 Debian 10 和 Ubuntu 20.04（glibc 2.28）。
- `Throne-<version>-debian-amd64-system-qt.deb` 不包含 Qt。它使用你的发行版提供的 Qt 6 库，并将其作为依赖安装。它需要 glibc 2.34 或更高版本以及 Qt 6，例如 Debian 12 和 Ubuntu 22.04 或更高版本。

请先使用普通软件包。如果普通软件包无法启动，例如在不支持 SSE4.2 的处理器上（[#845](https://github.com/throneproj/Throne/issues/845)），请使用 `system-qt` 软件包。arm64 也有同样的一对软件包（`debian-arm64.deb` 和 `debian-arm64-system-qt.deb`），用于 Fedora 和 RHEL 的 `.rpm` 软件包也是如此（`fedora-amd64.rpm`、`fedora-amd64-system-qt.rpm` 以及对应的 arm64 版本）。Throne 的 APT 和 RPM 软件源提供的是 `system-qt` 软件包。参见[安装](@/get_started/installation.zh.md#linux)。

## 哪些软件包是官方的？ {#third-party-packages}

Throne 开发者在 [GitHub 发布页面](https://github.com/throneproj/Throne/releases)上发布 Throne：ZIP 文件、Windows 安装程序，以及 `.deb` 和 `.rpm` 软件包（`.rpm` 自 1.3.0 起提供）。Throne 仓库中的 Linux 安装脚本安装的是发布页面上的 ZIP。

他们还在 `throneproj.github.io/Throne` 运行一个 APT 软件源和一个 RPM 软件源，提供每个稳定版的 `system-qt` 软件包；Throne 的发布工作流还会更新 AUR 中的 `throne` 软件包和 Homebrew cask `throneproj/tap/throne`。参见[安装](@/get_started/installation.zh.md#apt-repository)。

WinGet、Scoop 和 Nix/NixOS 软件包，以及 `throne-bin` 等其他 AUR 软件包，由社区成员制作，开发者不为其提供支持（[#1182](https://github.com/throneproj/Throne/issues/1182)、[#1622](https://github.com/throneproj/Throne/issues/1622)）。如果问题只在这类软件包中出现，请测试官方版本，并将打包问题报告给软件包的维护者。参见[包管理器](@/get_started/installation.zh.md#package-managers)。

## Windows 7、旧版 macOS、旧版 Linux 或旧处理器需要哪个版本？ {#old-systems}

- **Windows 7 SP1 和 Windows 8：** 使用 `windowslegacy64.zip`（64 位）或 `windows32.zip`（32 位）。通用安装程序同样可用：在 32 位 Windows 以及早于 Windows 10 1809 的 64 位 Windows 上，它会自动安装 legacy 版本。普通的 `windows64.zip` 需要 Windows 10 1809 或更高版本。在 Throne 1.3 及更早版本中，Windows 7 和 8 上的 TUN 模式只能使用 `gvisor` 协议栈（[#1291](https://github.com/throneproj/Throne/issues/1291)）；自 1.4.0 起，`Tun 设置`（Tun Settings）中已没有 `Stack` 设置。
- **运行 macOS 10.15 至 14.3 的 Intel Mac：** 使用 `macoslegacy-amd64.zip`。普通的 macOS 版本需要 macOS 14.4 或更高版本。更旧的 macOS 版本不受支持。在 Throne 1.3 及更早版本中，普通的 macOS 版本需要 macOS 13 或更高版本。
- **旧版 Linux 发行版：** 自 1.4.0 起，ZIP 文件、安装脚本以及普通的 `.deb` 和 `.rpm` 软件包需要 glibc 2.28 或更高版本，因此可在 Debian 10、Ubuntu 20.04、RHEL 8 和 Raspberry Pi OS bullseye 上运行。`-system-qt` 软件包（因此也包括 APT 和 RPM 软件源）需要 Debian 12、Ubuntu 22.04、RHEL 9 或更高版本。参见 [Linux](@/get_started/installation.zh.md#linux)。
- **Linux 上的旧处理器：** 如果 Throne 无法启动并输出 `This Qt build requires the following features: sse4.2 popcnt`，请安装 `system-qt` 软件包（[#845](https://github.com/throneproj/Throne/issues/845)）。

[下载](@/downloads.zh.md)页面的表格列出了每个文件的最低系统要求。

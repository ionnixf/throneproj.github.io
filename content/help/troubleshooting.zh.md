+++
title = "故障排除"
description = "针对 Throne 桌面版中 TUN 模式、DNS、订阅、系统代理、性能和启动问题的逐步检查。"
weight = 20
toc = true
+++

在下面找到你遇到的问题，并按顺序逐项检查。每个部分的末尾都附有一个链接，指向详细介绍该功能的指南。Throne for Android 的问题请参阅 [Android 故障排除](@/android/troubleshooting.zh.md)。如果所有方法都无效，请[报告 Bug](@/help/bug_reports.zh.md)。

自 1.4.0 起，全新安装会以简洁模式启动，该模式会隐藏本页提到的许多设置。要找到某项设置，请按 `Ctrl+K` 并输入它的名称，或者切换到高级模式。参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)。

## 首先检查 {#first-checks}

1. 通过 `程序`（Program）→ `检查更新`（Check For Update）更新 Throne（1.4.0 之前为 `工具`（Tools）→ `检查更新`）。如果该菜单项显示为灰色，请从[下载](@/downloads.zh.md)页面下载新版本。许多问题已在新版本中修复。
2. 查看主窗口底部的 `日志`（Logs）标签页。大多数错误都会显示在那里。
3. 测试配置档：选中它并按 `Ctrl+Shift+S`（`URL 测试选定项`，Url Test Selected）。如果测试失败，请先检查服务器和配置档，再去修改 TUN 或系统代理设置。
4. 尝试另一种模式。如果某个配置档在 `系统代理`（System Proxy）下可用，但在 `Tun 模式`（Tun Mode）下不可用，那么问题出在 TUN 设置上，而不是服务器上。

## TUN 模式 {#tun}

### TUN 已开启，但什么都打不开 {#tun-no-traffic}

1. 关闭 `Tun 模式`，然后用 `系统代理` 测试同一个配置档。如果也失败，请先修复配置档或服务器。
2. 关闭其他 VPN 应用。其他 VPN 和虚拟网卡可能会接管路由。
3. 检查是否有防火墙或杀毒软件阻止了 TUN 网卡；参见下面针对你的系统的说明。

**Windows：**

1. 允许 `ThroneCore` 通过 Windows 防火墙。第一次使用 TUN 模式时，Windows 会询问你。如果你当时拒绝了，Windows 会阻止核心，并且不会再次询问（[#1433](https://github.com/throneproj/Throne/issues/1433)）。请在防火墙设置中允许 `ThroneCore.exe`。
2. 带有网络防护功能的杀毒软件（例如 Avast 或 ESET）可能会在没有任何提示的情况下阻止 TUN 网卡（[#1687](https://github.com/throneproj/Throne/issues/1687)、[#1357](https://github.com/throneproj/Throne/issues/1357)）。请将 Throne 文件夹添加为例外，或者暂时关闭网络防护进行测试。
3. 暂时关闭 `Tun 设置` 中的 `严格路由`（Strict Route）进行测试。关闭后可能会出现 DNS 泄漏；参见 [Windows DNS 泄漏防护](@/advanced/windows_tun_mode.zh.md#strict-route)。

**Linux：**

1. 如果你在 systemd-resolved 中为整个系统开启了 DNS over TLS，TUN 模式会无法工作。请将其关闭（[#1146](https://github.com/throneproj/Throne/issues/1146)）。
2. 保持 `Tun 设置`（Tun Settings）中的 `自动重定向`（Auto Redirect）开启（仅在高级模式下可用）。它默认开启，可借助 nftables 改善 TUN 的路由和性能。

### TUN 无法启动 {#tun-start}

- **没有权限：** TUN 模式需要管理员或 root 权限。请接受 Throne 的请求，或参阅[为什么 TUN 模式需要管理员或 root 权限？](@/help/faq.zh.md#tun-admin)。如果开启了 `禁止权限请求`（Disable Privilege request）（仅在高级模式下可用），Throne 不会请求权限，你必须自行授予。
- **“严格的路由不可用”（Strict routing unavailable）**（Windows）：Windows 无法开启严格路由。打开 `Tun 设置`，关闭 `严格路由`，然后重新启动配置档。
- **“Tun 设备运行异常”（Tun device misbehaving）：** 虚拟网卡处于异常状态，例如在 Throne 被强制关闭之后。点击该消息中的 `重置`（Reset），或点击 `Tun 设置` 中的 `重启核心`（Restart Core），然后重新启动配置档。如果仍然失败，请重启电脑。
- **启动卡住**，并且 Throne 建议重启软件：在 Windows 上，非常大的 HOSTS 文件可能会导致刷新 DNS 缓存时卡住（[#1906](https://github.com/throneproj/Throne/issues/1906)）。请保持 HOSTS 文件精简。
- **“此计算机上的 Tun 由后台 Throne（PID …）占用；请停止它或使用 thronectl。”（A background Throne (PID …) owns Tun on this computer; stop it or use thronectl.）** 这台电脑上正在运行一个无界面 Throne。参见 [Throne 正在后台运行](#background-throne)。

### TUN 导致其他功能异常 {#tun-side-effects}

开启 TUN 模式时，移动热点、虚拟机、Docker 或公司 VPN 可能会无法正常工作。参见 [TUN 模式](@/guides/tun_mode.zh.md#side-effects)和[常用方案](@/guides/recipes.zh.md)。

详细信息：[TUN 模式](@/guides/tun_mode.zh.md#troubleshooting)。

## DNS {#dns}

### 网站打不开，或日志中显示 DNS 错误 {#dns-errors}

1. 打开 `设置` → `路由设置`（Routing Settings）→ `DNS`。在简洁模式下，这些字段位于 `通用`（Common）标签页的 `简易 DNS 设置`（Simple DNS Settings）区域中。`远程 DNS`（Remote DNS）必须能够通过你的代理访问，`直连 DNS`（Direct DNS）必须能够在不使用代理的情况下工作。可以尝试其中一个预设服务器。
2. 如果你的网络不支持 IPv6，请不要在 `通用`（Common）标签页的 `默认域策略`（Default Domain Strategy）或 `解析域策略`（Resolve Domain Strategy）（仅在高级模式下可用）中使用 `ipv6_only`（[#1743](https://github.com/throneproj/Throne/issues/1743)）。请将它们留空，或者使用 `prefer_ipv4`。
3. **Linux：** 在 systemd-resolved 中为整个系统开启 DNS over TLS 会导致 TUN 模式失效；参见 [TUN 已开启，但什么都打不开](#tun-no-traffic)。

### 泄漏测试显示了我的运营商的 DNS {#dns-leak}

- 匹配 `direct` 规则的域名会使用 `直连 DNS` 解析，而它通常就是你的运营商（ISP）的 DNS。这是正常的；参见 [DNS 泄漏测试](@/guides/dns.zh.md#leak-tests)。
- 在系统代理模式下，应用可以自行进行 DNS 查询，这些查询根本不会到达 Throne。请使用 TUN 模式。
- WebRTC 测试没有显示任何 IP 地址并不是泄漏。只有显示出你的真实 IP 地址才算泄漏（[#1267](https://github.com/throneproj/Throne/issues/1267)）。
- **Windows：** 参见 [Windows DNS 泄漏防护](@/advanced/windows_tun_mode.zh.md#strict-route)。

详细信息：[DNS](@/guides/dns.zh.md)。

## 订阅 {#subscriptions}

### 更新失败 {#sub-update-fails}

日志中会显示 `请求订阅 … 时出错: …`（`Requesting subscription … error: …`）以及失败原因。

1. **“有用代理的请求，但未启动配置档。”（Request with proxy but no profile started.）** 当 `基本设置`（Basic Settings）→ `杂项`（Miscellaneous）中的 `使用代理`（Use proxy）开启时（在简洁模式下，它位于 `通用`（Common）标签页的 `网络设置`（Network Settings）区域中），以及每当 `系统代理` 开启时，Throne 都会通过自身的代理发送它自己的请求。请先启动一个配置档，或者将两者都关闭。如果你开启了 `禁用混合入站`（Disable Mixed Inbound），请关闭 `使用代理`（[#1689](https://github.com/throneproj/Throne/issues/1689)）。
2. **服务商的网站在你所在的国家被封锁：** 启动一个可用的配置档，开启 `使用代理`，然后重新更新。
3. **服务器返回 `403`，或者没有返回有用的内容：** 许多服务商只响应它们认识的应用。请设置服务商要求的 `User Agent`：在 `基本设置` → `订阅`（Subscription）中为所有分组设置，或者自 1.3.1 起，在 `分组`（Groups）→ `编辑当前分组`（Edit current Group）→ `高级`（Advanced）中为单个分组设置（两者都仅在高级模式下可用）。默认情况下，Throne 发送的是 `Throne/<version>`。
4. **“Device fingerprint required”（需要设备指纹）或类似消息：** 服务商需要你的 HWID。请开启 HWID 发送；参见[常见问题](@/help/faq.zh.md#hwid)。

### 更新后找不到配置档 {#sub-no-profiles}

日志显示“No profiles found in the subscription”（订阅中未找到配置档），分组保持不变。服务商的响应中没有任何 Throne 能够读取的内容。

1. 检查该链接是否为订阅 URL，而不是网页或应用链接。以 `happ://crypt` 或 `v2raytun://crypt` 开头的链接是专为这些应用加密的，Throne 无法读取。请向服务商索要普通的订阅 URL。
2. 尝试其他 `User Agent`。对于不认识的应用，服务商经常会发送不同的格式，或者什么都不发送。

### 分组不更新 {#sub-no-update}

- 右键点击分组的标签页 → `更新订阅`（Update subscription），或者使用 `分组` → `更新订阅`（`Ctrl+U`）来更新分组。
- 通过 `添加配置档到这个分组`（Add profiles to this group）导入的配置档只添加了一次，没有订阅 URL。要获得更新，请再次按 `Ctrl+V` 粘贴该 URL，并选择 `创建新的订阅分组`（Create new subscription group）。
- 自动更新需要开启 `基本设置` → `订阅` → `订阅自动更新`（Subscription auto update）（在简洁模式下，它位于 `通用`（Common）标签页的底部），该选项默认关闭。开启了 `跳过自动更新`（Skip automatic update）的分组不会自动更新。

更新后配置档发生了变化或消失了？参见[为什么订阅更新后我的配置档停止了，或者仍在运行？](@/help/faq.zh.md#profile-after-update)

详细信息：[订阅与分组](@/guides/subscriptions.zh.md#problems)。

## 系统代理 {#system-proxy}

### 有些应用忽略系统代理 {#apps-ignore-proxy}

应用可能遵循系统代理，也可能忽略它。PowerShell 等终端、Windows 应用商店应用、Discord 以及 Telegram 通话通常会忽略系统代理（[#370](https://github.com/throneproj/Throne/issues/370)、[#1631](https://github.com/throneproj/Throne/issues/1631)）。路由规则只作用于到达 Throne 的流量，因此请为这些应用使用 `Tun 模式`。参见[该用哪种模式？](@/guides/proxy_modes.zh.md#which-mode)

### 系统代理没有被设置 {#proxy-not-set}

1. 启动一个配置档。Throne 只在配置档运行期间设置系统代理。
2. 关闭 `基本设置` → `通用`（Common）中的 `禁用混合入站`。开启该选项时，Throne 会显示“Cannot set system proxy when mixed inbound is disabled.”（禁用混合入站时无法设置系统代理）。
3. **Linux：** Throne 只能在 GNOME 和 KDE 上设置系统代理。在其他桌面环境中，请将状态栏中显示的地址（`Mixed: …`）填入系统设置，或者使用 TUN 模式。请以你的普通用户身份运行 Throne，而不是 root（[#864](https://github.com/throneproj/Throne/issues/864)）。
4. **Windows：** 如果某个应用需要其他格式的代理地址，请修改 `基本设置` → `通用` 中的 `代理格式`（Proxy Format）（仅在高级模式下可用）。

### 关闭 Throne 后无法上网 {#no-internet-after-exit}

如果 Throne 在 `系统代理` 开启时被强制关闭，系统代理仍会指向 Throne。参见[常见问题](@/help/faq.zh.md#force-quit)。

详细信息：[系统代理、TUN 与局域网共享](@/guides/proxy_modes.zh.md#system-proxy)。

## 性能和崩溃 {#performance}

### CPU 或内存占用过高 {#high-cpu}

1. 控制配置档的数量，例如总数少于 10,000 个。成千上万的配置档会让 Throne 变慢并占用大量内存。在 URL 测试之后，用 `分组` → `清理`（Clean up）→ `移除不可用项`（Remove Unavailable）删除失效的配置档，或者让每个分组在更新后通过其 `高级`（Advanced）设置自动清理（先 `运行 URL 测试`（Run URL test），再 `移除不可用配置档`（Remove unavailable profiles）；仅在高级模式下可用）。
2. BT 下载客户端会打开非常多的连接。开启多路复用（Multiplex）时，这可能会占用大量 CPU（[#1090](https://github.com/throneproj/Throne/issues/1090)）。多路复用默认关闭。如果你开启过它，请在配置档中将其关闭，或者关闭 `设置` → `预设设置`（Preset Settings）→ `多路复用`（Multiplex）中的 `默认开启`（Default On）（仅在高级模式下可用）。
3. 取消勾选 `基本设置` → `样式`（Style）→ `连接统计`（Connection statistics）→ `启用`（Enable）（仅在高级模式下可用）。这样 Throne 将不再查找每个连接背后的程序，在连接很多时可以节省 CPU。关闭期间，`连接`（Connections）标签页会保持为空。
4. **Windows：** 在使用大型规则集时，`启用 Tun 路由`（Enable Tun Routing）可能会导致 CPU 占用非常高。请在 `Tun 设置` 中将其关闭。

### Throne 或核心崩溃 {#crashes}

1. 核心停止时，Throne 会将其与你的配置档一起重新启动。如果核心在 10 秒内再次停止，Throne 会放弃，并在日志中记录“核心退出太频繁，停止自动重启这个配置档”（Core exits too frequently, stop automatic restart this profile）。
2. Throne 本身崩溃后，下次启动时日志中会显示“上次 Throne 没有正常干净的关闭”（Throne did not shut down cleanly last time）。崩溃会话的日志会以 `logs/crashed-<date>-<time>.log` 的形式保存在配置文件夹中。在 Windows 上，Throne 还会在 `crashes` 文件夹中保存一份崩溃报告（一个 `.txt` 文件和一个 `.dmp` 文件）。
3. 使用全新配置进行测试：退出 Throne，重命名 `config` 文件夹，然后启动 Throne，它会创建一个新的空配置。`config` 文件夹的位置可以在高级模式下通过 `设置` → `打开配置文件夹`（Open Config Folder）查看。添加一个配置档后再次测试。如果不再崩溃，说明是你的旧配置导致了崩溃。要恢复原状，请退出 Throne，删除新的 `config` 文件夹，再把旧文件夹改回原名。参见[命令行、文件与日志](@/reference/files.zh.md#reset)。
4. 报告崩溃并附上这些文件：[报告 Bug](@/help/bug_reports.zh.md#logs)。

## 启动和桌面问题 {#startup}

### Throne 在 Linux 上无法启动 {#linux-start}

从终端启动 Throne 以查看错误信息。对于 `.deb` 和 `.rpm` 软件包：

```bash
/opt/Throne/Throne -appdata
```

对于 ZIP 版，请在其文件夹中运行 `./Throne`。然后查看提示信息：

- `Could not load the Qt platform plugin "xcb"`，并提示需要 `libxcb-cursor0`：安装 `libxcb-cursor0`（Debian、Ubuntu）或 `xcb-util-cursor`（Fedora），参见 [#1096](https://github.com/throneproj/Throne/issues/1096)。ZIP 文件和普通软件包已自带该库，因此只有使用系统 Qt 的 Throne 才会出现这条提示。
- `This Qt build requires the following features: sse4.2 popcnt`：改为安装 `system-qt` 软件包（[常见问题](@/help/faq.zh.md#old-systems)）。
- 其他 Qt 错误：尝试使用 `system-qt` 软件包，它使用你的发行版提供的 Qt。

不要用 `sudo` 启动 Throne。只有核心需要 root 权限（[常见问题](@/help/faq.zh.md#linux-suid)）。

### GNOME 上没有托盘图标 {#gnome-tray}

除非安装并启用了 AppIndicator 扩展，否则 GNOME 不会显示托盘图标。请安装一个这样的扩展，以显示 Throne 的托盘图标。如果 Throne 窗口处于隐藏状态，请再次启动 Throne：正在运行的实例会显示它的窗口。你也可以开启 `基本设置` → `样式` → `禁用托盘`（Disable tray），这样关闭窗口就会退出 Throne。

### macOS 提示无法打开 Throne {#macos-open}

Throne 没有使用 Apple 证书签名，因此 macOS 会阻止下载的应用。

1. 将 `Throne.app` 移动到 `/Applications`。
2. 在“终端”（Terminal）中移除隔离标记：

   ```bash
   xattr -d com.apple.quarantine /Applications/Throne.app
   ```

3. 再次打开 Throne。

### 重启后 Throne 没有重新连接 {#reconnect}

开启 `程序`（Program）→ `记住上次的配置档`（Remember last profile）。下次启动时，Throne 会再次启动上次使用的配置档；如果退出时 `系统代理` 和 `Tun 模式` 处于开启状态，也会重新开启它们。要让 Throne 随系统一起启动，请开启 `程序` → `随系统启动`（Start with system）。

**Windows：** Throne 以管理员身份运行过一次之后，每次启动都会请求管理员权限。`随系统启动` 使用一个任务计划程序任务，以这些权限启动 Throne 而不再询问。要在没有管理员权限的情况下启动，请开启 `基本设置` → `安全`（Security）→ `始终以标准用户身份启动`（Always Start as Standard User）（仅在高级模式下可用）。

### `throne://` 链接没有反应 {#deeplinks}

- **Windows 和 Linux：** ZIP 版默认不注册 `throne://` 链接（在 Linux 上自 1.4.0 起）。请开启 `基本设置` → `通用` → `启动时注册 throne:// 链接`（Register throne:// links at startup），或点击它旁边的 `安装`（Install）（仅在高级模式下可用）。
- 更多检查：[深度链接](@/advanced/deeplinks.zh.md#troubleshooting)。

### Throne 正在后台运行 {#background-throne}

自 1.4.0 起，Throne 也可以作为[无界面 Throne](@/headless/_index.zh.md) 在没有窗口的情况下运行，例如作为 systemd 服务或 systemd 用户单元。Throne 窗口会察觉到同一台电脑上的无界面 Throne：

- **Throne 无法启动，并提示“Throne is running in the background (PID …). Stop the service or use thronectl.”（Throne 正在后台运行（PID …）。请停止该服务或使用 thronectl。）** 有一个无界面 Throne 正在使用同一个数据文件夹，例如使用 `~/.config/Throne` 的 systemd 用户单元。请停止它（例如运行 `systemctl --user stop throne`），然后重新启动 Throne 窗口。或者让它继续运行，并用 [thronectl](@/advanced/api.zh.md) 管理它。
- **启动时，日志显示“此计算机上正在运行后台 Throne（PID …，数据目录：…）”（A background Throne (PID …) is running on this computer），并有一条通知显示“Throne 正在后台运行”（Throne is running in the background）。** 这台电脑上正在运行一个使用其他数据文件夹的无界面 Throne。Throne 窗口可以正常工作，但无法使用 TUN 模式。
- **无法开启 `Tun 模式`（Tun Mode）：“此计算机上的 Tun 由后台 Throne（PID …）占用；请停止它或使用 thronectl。”（A background Throne (PID …) owns Tun on this computer; stop it or use thronectl.）** 一台电脑上同一时间只能有一个 Throne 使用 TUN 模式。无界面 Throne 运行期间，Throne 窗口会拒绝开启 TUN 模式，即使无界面 Throne 本身没有使用 TUN。要在窗口中使用 TUN 模式，请停止无界面 Throne。
- **`检查更新`（Check For Update）没有 `更新`（Update）按钮，并提示“从此安装目录运行的后台 Throne（PID …）仍在运行，无法更新；请先停止它。”（Update is unavailable while a background Throne (PID …) runs from this installation; stop it first.）** 无界面 Throne 运行的程序文件正是更新将要替换的文件。请停止它，完成更新，然后再次启动它。

如果 Throne 提示“Another Throne instance (PID …) is using this data directory”（另一个 Throne 实例（PID …）正在使用此数据目录），说明另一个 Throne 占用了该数据文件夹，但没有在 10 秒内响应，例如因为它卡住了。请退出它，或结束该 PID 对应的进程，然后再次启动 Throne。

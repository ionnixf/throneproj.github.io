+++
title = "故障排除"
description = "Throne 桌面版中 TUN 模式、DNS、订阅、系统代理、性能和启动问题的解决方法。"
weight = 20
toc = true
+++

找到你遇到的问题，并按顺序逐项检查。本页提到的设置在简洁模式下可能被隐藏：按 `Ctrl+K` 并输入设置名称即可找到（[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）。Android 版请参阅 [Android 故障排除](@/android/troubleshooting.zh.md)。如果所有方法都无效，请[报告 Bug](@/help/bug_reports.zh.md)。

## 首先检查 {#first-checks}

1. 通过 `程序`（Program）→ `检查更新`（Check For Update）更新 Throne；如果该菜单项显示为灰色，请从[下载](@/downloads.zh.md)页面下载新版本。
2. 查看主窗口底部的 `日志`（Logs）标签页。
3. 用 `URL 测试选定项`（Url Test Selected，`Ctrl+Shift+S`）测试配置档。如果测试失败，请先修复服务器或配置档。
4. 如果某个配置档在 `系统代理`（System Proxy）下可用，但在 `Tun 模式`（Tun Mode）下不可用，那么问题出在 TUN 设置上。

## TUN 模式 {#tun}

### TUN 已开启，但什么都打不开 {#tun-no-traffic}

1. 关闭其他 VPN 应用。它们可能会接管路由。
2. **Windows：** 在 Windows 防火墙中允许 `ThroneCore.exe`。如果 Windows 询问时你拒绝了，Windows 会一直阻止核心，并且不会再次询问（[#1433](https://github.com/throneproj/Throne/issues/1433)）。
3. **Windows：** 杀毒软件的网络防护（例如 Avast 或 ESET）可能会在没有任何提示的情况下阻止 TUN 网卡（[#1687](https://github.com/throneproj/Throne/issues/1687)、[#1357](https://github.com/throneproj/Throne/issues/1357)）。请将 Throne 文件夹添加为例外。
4. **Windows：** 暂时关闭 `Tun 设置`（Tun Settings）中的 `严格路由`（Strict Route）进行测试。关闭后可能会出现 DNS 泄漏；参见 [Windows DNS 泄漏防护](@/advanced/windows_tun_mode.zh.md#strict-route)。
5. **Linux：** 关闭 systemd-resolved 中为整个系统开启的 DNS over TLS（[#1146](https://github.com/throneproj/Throne/issues/1146)），并保持 `Tun 设置` 中的 `自动重定向`（Auto Redirect）开启。

### TUN 无法启动 {#tun-start}

- **没有权限：** 接受 Throne 对管理员或 root 权限的请求（[常见问题](@/help/faq.zh.md#tun-admin)）。如果开启了 `禁止权限请求`（Disable Privilege request），你必须自行授予权限。
- **“严格的路由不可用”（Strict routing unavailable）**（Windows）：关闭 `Tun 设置` 中的 `严格路由`，然后重新启动配置档。
- **“Tun 设备运行异常”（Tun device misbehaving）：** 点击该消息中的 `重置`（Reset），或点击 `Tun 设置` 中的 `重启核心`（Restart Core），然后重新启动配置档。如果仍然失败，请重启电脑。
- **启动卡住**（Windows）：非常大的 HOSTS 文件可能会导致刷新 DNS 缓存时卡住（[#1906](https://github.com/throneproj/Throne/issues/1906)）。请保持 HOSTS 文件精简。
- **“此计算机上的 Tun 由后台 Throne（PID …）占用；请停止它或使用 thronectl。”（A background Throne (PID …) owns Tun on this computer; stop it or use thronectl.）** 参见 [Throne 正在后台运行](#background-throne)。

### TUN 导致其他功能异常 {#tun-side-effects}

开启 TUN 模式时，移动热点、虚拟机、Docker 或公司 VPN 可能会无法正常工作。参见 [TUN 模式](@/guides/tun_mode.zh.md#side-effects)和[常用方案](@/guides/recipes.zh.md)。

## DNS {#dns}

### 网站打不开，或日志中显示 DNS 错误 {#dns-errors}

1. 在 `设置` → `路由设置`（Routing Settings）→ `DNS` 中，`远程 DNS`（Remote DNS）必须能够通过你的代理访问，`直连 DNS`（Direct DNS）必须能够在不使用代理的情况下工作。可以尝试其中一个预设服务器。
2. 如果你的网络不支持 IPv6，请不要在 `通用`（Common）标签页的 `默认域策略`（Default Domain Strategy）或 `解析域策略`（Resolve Domain Strategy）中使用 `ipv6_only`（[#1743](https://github.com/throneproj/Throne/issues/1743)）。请将它们留空，或者使用 `prefer_ipv4`。

### 泄漏测试显示了我的运营商的 DNS {#dns-leak}

这通常是正常的；参见[常见问题](@/help/faq.zh.md#dns-leak-test)。

## 订阅 {#subscriptions}

### 更新失败 {#sub-update-fails}

日志中会显示 `请求订阅 … 时出错: …`（`Requesting subscription … error: …`）以及失败原因。

- **“有用代理的请求，但未启动配置档。”（Request with proxy but no profile started.）** 当 `使用代理`（Use proxy，位于 `基本设置`（Basic Settings）→ `杂项`（Miscellaneous））或 `系统代理` 开启时，Throne 会通过自身的代理发送它自己的请求。请先启动一个配置档，或者将两者都关闭。如果开启了 `禁用混合入站`（Disable Mixed Inbound），请关闭 `使用代理`（[#1689](https://github.com/throneproj/Throne/issues/1689)）。
- **服务商的网站在你所在的国家被封锁：** 启动一个可用的配置档，开启 `使用代理`，然后重新更新。
- **服务器返回 `403`，或者没有返回有用的内容：** 许多服务商只响应它们认识的应用。请设置服务商要求的 `User Agent`：在 `基本设置` → `订阅`（Subscription）中为所有分组设置，或在 `分组`（Groups）→ `编辑当前分组`（Edit current Group）→ `高级`（Advanced）中为单个分组设置。默认值为 `Throne/<version>`。
- **“Device fingerprint required”（需要设备指纹）或类似消息：** 服务商需要你的 [HWID](@/help/faq.zh.md#hwid)。

### 更新后找不到配置档 {#sub-no-profiles}

日志显示“No profiles found in the subscription”（订阅中未找到配置档）。请检查该链接是否为订阅 URL，而不是网页或应用链接：`happ://crypt` 和 `v2raytun://crypt` 链接是专为这些应用加密的，Throne 无法读取。也可以尝试其他 `User Agent`。

### 分组不更新 {#sub-no-update}

- 通过 `添加配置档到这个分组`（Add profiles to this group）添加的配置档没有订阅 URL。请再次按 `Ctrl+V` 粘贴该 URL，并选择 `创建新的订阅分组`（Create new subscription group）。
- 自动更新需要开启 `基本设置` → `订阅` → `订阅自动更新`（Subscription auto update），该选项默认关闭；开启了 `跳过自动更新`（Skip automatic update）的分组会被跳过。

更新后配置档发生了变化或消失了？参见[常见问题](@/help/faq.zh.md#profile-after-update)和[订阅与分组](@/guides/subscriptions.zh.md#problems)。

## 系统代理 {#system-proxy}

### 有些应用忽略系统代理 {#apps-ignore-proxy}

PowerShell 等终端、Windows 应用商店应用、Discord 以及 Telegram 通话通常会忽略系统代理（[#370](https://github.com/throneproj/Throne/issues/370)、[#1631](https://github.com/throneproj/Throne/issues/1631)）。请为这些应用使用 `Tun 模式`；参见[该用哪种模式？](@/guides/proxy_modes.zh.md#which-mode)

### 系统代理没有被设置 {#proxy-not-set}

1. 启动一个配置档。Throne 只在配置档运行期间设置系统代理。
2. **“Cannot set system proxy when mixed inbound is disabled.”（禁用混合入站时无法设置系统代理）** 关闭 `基本设置` → `通用`（Common）中的 `禁用混合入站`。
3. **Linux：** Throne 只能在 GNOME 和 KDE 上设置系统代理。在其他桌面环境中，请将状态栏中显示的 `Mixed: …` 地址填入系统设置，或者使用 TUN 模式。不要以 root 身份运行 Throne（[#864](https://github.com/throneproj/Throne/issues/864)）。

### 关闭 Throne 后无法上网 {#no-internet-after-exit}

如果 Throne 在 `系统代理` 开启时被强制关闭，系统代理仍会指向 Throne。参见[常见问题](@/help/faq.zh.md#force-quit)。

## 性能和崩溃 {#performance}

### CPU 或内存占用过高 {#high-cpu}

1. 让配置档总数少于约 10,000 个。在 URL 测试之后，用 `分组` → `清理`（Clean up）→ `移除不可用项`（Remove Unavailable）删除失效的配置档。
2. 开启多路复用（Multiplex）时，BT 下载客户端可能会占用大量 CPU（[#1090](https://github.com/throneproj/Throne/issues/1090)）。多路复用默认关闭；如果你开启过它，请在配置档中将其关闭，或者关闭 `设置` → `预设设置`（Preset Settings）→ `多路复用`（Multiplex）中的 `默认开启`（Default On）。
3. 取消勾选 `基本设置` → `样式`（Style）→ `连接统计`（Connection statistics）→ `启用`（Enable）。之后 `连接`（Connections）标签页会保持为空。
4. **Windows：** 在使用大型规则集时，`启用 Tun 路由`（Enable Tun Routing）可能会导致 CPU 占用非常高。请在 `Tun 设置` 中将其关闭。

### Throne 或核心崩溃 {#crashes}

- **“核心退出太频繁，停止自动重启这个配置档”（Core exits too frequently, stop automatic restart this profile）。** 核心反复崩溃，因此 Throne 不再用你的配置档重启它。
- 下次启动时显示 **“上次 Throne 没有正常干净的关闭”（Throne did not shut down cleanly last time）**：Throne 崩溃了。该会话的日志会以 `logs/crashed-<date>-<time>.log` 的形式保存在配置文件夹中；在 Windows 上，`crashes` 文件夹中还会保存一份崩溃报告。
- 要排除配置的问题，请退出 Throne，重命名 `config` 文件夹，然后重新启动 Throne；参见[命令行、文件与日志](@/reference/files.zh.md#reset)。
- 报告崩溃并附上这些文件：[报告 Bug](@/help/bug_reports.zh.md#logs)。

## 启动和桌面问题 {#startup}

### Throne 无法启动 {#no-start}

- **“Incompatible processor. This Qt build requires the following features: sse4.2 popcnt”（处理器不兼容，此 Qt 版本需要 sse4.2 和 popcnt 指令）：** 在 Windows 上使用 `windowslegacy64.zip`，在 Linux 上使用 `-system-qt` 软件包（[常见问题](@/help/faq.zh.md#old-systems)）。
- **Linux：`Could not load the Qt platform plugin "xcb"`：** 如果 Throne 使用的是系统的 Qt，请安装 `libxcb-cursor0`（Debian、Ubuntu）或 `xcb-util-cursor`（Fedora、RHEL）（[#1096](https://github.com/throneproj/Throne/issues/1096)）。如果 ZIP 版或普通软件包出现此错误，请使用 `-system-qt` 软件包，它也能解决其他 Qt 错误。
- **macOS：“Throne is damaged and can't be opened”（Throne 已损坏，无法打开）：** Throne 没有使用 Apple 证书签名。将 `Throne.app` 移动到 `/Applications`，在“终端”（Terminal）中移除隔离标记，然后再次打开 Throne：

  ```bash
  xattr -d com.apple.quarantine /Applications/Throne.app
  ```

在 Linux 上，从终端启动 Throne 以查看错误信息：软件包使用 `/opt/Throne/Throne -appdata`，ZIP 版则在其文件夹中运行 `./Throne`。不要使用 `sudo`；只有核心需要 root 权限（[常见问题](@/help/faq.zh.md#linux-suid)）。

### GNOME 上没有托盘图标 {#gnome-tray}

安装并启用 AppIndicator 扩展。如果窗口处于隐藏状态，请再次启动 Throne 以显示窗口，或者开启 `基本设置` → `样式` → `禁用托盘`（Disable tray），这样关闭窗口就会退出 Throne。

### 重启后 Throne 没有重新连接 {#reconnect}

开启 `程序`（Program）→ `记住上次的配置档`（Remember last profile）。下次启动时，Throne 会再次启动退出时正在运行的配置档，并将 `系统代理` 和 `Tun 模式` 恢复为当时的状态。`程序` → `随系统启动`（Start with system）会在你登录时启动 Throne；在 Windows 上，如果 Throne 以管理员身份运行，它还会在不询问的情况下获得管理员权限（[常见问题](@/help/faq.zh.md#tun-admin)）。

### `throne://` 链接没有反应 {#deeplinks}

在 Windows 和 Linux 上，ZIP 版默认不注册 `throne://` 链接。请开启 `基本设置` → `通用` → `启动时注册 throne:// 链接`（Register throne:// links at startup），或点击它旁边的 `安装`（Install）。更多检查：[深度链接](@/advanced/deeplinks.zh.md#troubleshooting)。

### Throne 正在后台运行 {#background-throne}

[无界面 Throne](@/headless/_index.zh.md)（例如 systemd 服务）可以与 Throne 窗口运行在同一台电脑上：

- **“Throne is running in the background (PID …). Stop the service or use thronectl.”（Throne 正在后台运行（PID …）。请停止该服务或使用 thronectl。）**，并且窗口无法打开：无界面 Throne 使用的是同一个数据文件夹。请停止它（例如运行 `systemctl --user stop throne`），然后重新启动 Throne，或者用 [thronectl](@/advanced/api.zh.md) 管理它。
- **“此计算机上正在运行后台 Throne（PID …，数据目录：…）”（A background Throne (PID …) is running on this computer）：** 正在运行一个使用其他数据文件夹的无界面 Throne。Throne 窗口可以正常工作，但无法使用 TUN 模式。
- **“此计算机上的 Tun 由后台 Throne（PID …）占用；请停止它或使用 thronectl。”（A background Throne (PID …) owns Tun on this computer; stop it or use thronectl.）** 只要有任何无界面 Throne 在运行，窗口就无法使用 TUN 模式，即使它本身没有使用 TUN。请停止无界面 Throne。
- **“从此安装目录运行的后台 Throne（PID …）仍在运行，无法更新；请先停止它。”（Update is unavailable while a background Throne (PID …) runs from this installation; stop it first.）** 请停止它，完成更新，然后再次启动它。
- **“Another Throne instance (PID …) is using this data directory”（另一个 Throne 实例（PID …）正在使用此数据目录）：** 另一个 Throne 占用了该数据文件夹，但没有响应。请退出它或结束其进程，然后再次启动 Throne。

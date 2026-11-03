+++
title = "Android 故障排除"
description = "解决 Throne for Android 的常见问题：VPN 无法启动或没有流量、在后台被停止，以及订阅和路由错误。"
weight = 60
toc = true
+++

找到你看到的消息或症状，然后按照相应步骤操作。消息按应用简体中文界面的显示引用；应用中尚未翻译的消息保留英文原文，并在括号中附上中文译文。有关 Throne 桌面版的问题，请参见[故障排除](@/help/troubleshooting.zh.md)。

## VPN 无法启动或没有流量 {#connection}

启动失败时，Throne 会在屏幕底部的提示条中显示原因，并附带 `日志`（Logs）按钮。该消息会一直显示，直到你将其滑走、点按 `日志` 或再次启动。

### “VPN 服务权限请求被拒绝” {#vpn-denied}

Android 没有授予 Throne VPN 权限。通常是你拒绝了请求。当另一个 VPN 应用被设为始终开启的 VPN 时，Android 也会不经询问直接拒绝。

1. 再次点按连接按钮，并允许请求。
2. 如果没有出现请求，请打开 Android 的 VPN 设置，为另一个应用关闭始终开启的 VPN。
3. 如果 Throne 显示“无法请求 VPN 确认”，说明你的设备没有用于此请求的界面。参见 [VPN 权限](@/android/permissions.zh.md#vpn-permission)。

### Android 拒绝创建 VPN {#vpn-failed}

Android 授予了权限，但随后拒绝创建 VPN 接口。此时“失败: …”消息中通常包含“configure tun interface”（配置 TUN 接口）。

1. 断开其他 VPN 应用，并在 Android 的 VPN 设置中确认它们都没有被设为始终开启的 VPN。
2. 重启设备并重新连接。

### 连接时出现的其他消息 {#start-errors}

| 消息 | 原因 | 解决方法 |
| --- | --- | --- |
| “请选择一个服务器配置” | 没有选择配置。 | 点按一个配置，然后连接。 |
| “失败: Profile … has a type this build cannot use: …”（配置 … 的类型在此版本中无法使用：…） | 该配置类型仅存在于桌面版，例如 Tailscale 或额外核心（Extra Core）。 | 使用其他配置。参见[下文](#desktop-vs-android)。 |
| “无法连接：网络的 DNS (…) 未应答。请设置可用的直连 DNS。”或“无法连接：系统 DNS 未应答。请设置可用的直连 DNS。” | 自 2.0.1 起。连接启动时，`直连 DNS`（Direct DNS）没有应答。使用默认的 `直连 DNS`（`localhost`）时，Throne 会自己询问当前网络的 DNS 服务器。参见 [Android 上的 DNS](@/guides/dns.zh.md#android)。 | 点按消息中的 `DNS`，将 `直连 DNS` 设为在此网络上能够应答的 DNS 服务器，例如 `tls://1.1.1.1`。 |
| “无法下载规则集：…” | 自 2.0.2 起。某条路由规则使用了 Throne 之前没有下载过的规则集，而下载失败了。 | 点按 `仍然连接`（Connect anyway），然后点按 `连接`（Connect）。在 Throne 下载这些规则集之前，使用它们的规则不会匹配任何内容。参见 [Android 上的路由](@/guides/routing.zh.md#android)。 |
| “无法从 … 下载 geoip.dat：…”或“… geosite.dat …”，后面跟着“请检查「设置 › 核心 › Xray Geo 资源」中的来源，或使用其他配置连接后在那里点按「立即下载」。” | 自 2.0.2 起。该配置是带有 `geoip:` 或 `geosite:` 规则的 Xray 配置，而 Throne 无法下载它需要的文件。 | 点按 `Geo 资源`（Geo assets）。使用其他配置连接，然后点按 `立即下载`（Download now），或选择其他 `来源`（Source）。参见[配置在桌面版上可用，但在 Android 上不可用](#desktop-vs-android)。 |
| 与路由或 WARP 有关的消息 | 路由配置或 WARP 设置中存在问题。 | 参见[路由错误](#routing)和 [WARP 错误](#warp)。 |
| 其他任何“失败: …”消息 | 核心报告了错误。 | 点按 `日志`（Logs），阅读最后几行。 |

### 已连接，但无法加载任何内容 {#no-traffic}

1. 点按屏幕底部的横栏。“连接成功: HTTP 握手耗时 …ms”（自 2.0.3 起为“HTTP：… 毫秒”）表示该配置可用。“失败: …”表示该配置或其服务器不可用。
2. 如果测试失败，请点按 ⋮ → `测试`（Test）→ `URL 测试`（URL test）（在 2.0.4 及更早版本中：⋮ → `URL 测试`），然后选择一个可用的配置。参见[测试与自动选择器](@/guides/testing.zh.md)。
3. 检查分应用代理：打开 `设置`（Settings）→ `TUN / VPN` → `分应用代理`（Apps VPN mode）查看应用列表。自 2.0.5 起，在它开启时点按会关闭分应用代理；再次点按即可打开列表。选择 `代理`（Proxy）时，只有所选应用使用 VPN；选择 `绕过`（Bypass）时，所选应用不使用 VPN。如果没有选择任何应用，所有应用都会使用 VPN。参见[分应用代理](@/android/modes.zh.md#per-app-proxy)。
4. 通过 `设置` → `路由`（Routing）→ `当前路由配置`（Current routing profile）（在 2.0.4 及更早版本中：⋮ → `路由配置`（Routing profile））切换到 `Default` 路由配置，然后再次测试。如果现在可以正常工作，问题出在你自己的路由配置中的某条规则。如果 Throne 提示“分组 "…" 使用自己的路由配置，因此正在运行的配置会继续使用它。”（自 2.0.5 起），请改为在该分组的设置中将 `路由配置`（Routing profile）设为 `默认（…）`（Default (…)）。参见[路由](@/guides/routing.zh.md)。
5. 检查 DNS：`设置` → `DNS` → `远程 DNS`（Remote DNS），默认为 `https://8.8.8.8/dns-query`。请尝试其他服务器。自 2.0.4 起，路由配置可以使用自己的 DNS 服务器（其 `DNS` 部分中的 `使用自定义 DNS 设置`（Use custom DNS settings））。此时 `设置` → `DNS` 会提示当前路由配置使用自己的 DNS 服务器；请改为在该路由配置中修改它们。参见 [DNS](@/guides/dns.zh.md)。
6. 如果你在 `设置` → `TUN / VPN` 中开启了 `Tun IPv6` 或 `Tun 路由`（Tun routing），请关闭它们后再次测试。仅当你的服务器支持 IPv6 时才使用 `Tun IPv6`。如果某些应用或网站无法访问而其他的正常，请在同一界面中将 `MTU` 设为 `1500` 并重新连接。
7. 要确定问题是否由 VPN 模式引起，请将 `设置` → `通用`（General）→ `运行模式`（Service mode）设为 `仅代理`（Proxy only）并连接。然后在一个自带代理设置的应用中，将代理设为 SOCKS5 代理 `127.0.0.1`、端口 `2080`（参见 `设置` → `入站`（Inbound）→ `代理端口`（Proxy port））。如果该应用可以正常工作，说明问题出在 VPN 模式：请切换回 `VPN` 模式，再检查一遍第 3 步和第 6 步。在 2.0.1 及更早版本中，还可以尝试其他 `TUN 实现`（TUN implementation）。参见 [VPN 与代理模式](@/android/modes.zh.md#tun-settings)。

### Throne 关闭时无法上网 {#blocked-without-vpn}

Android 的“始终开启的 VPN”与“屏蔽未使用 VPN 的所有连接”（Block connections without VPN）同时处于开启状态。此时只要 Throne 未连接，Android 就会阻止所有流量。请使用可用的配置连接 Throne，或在 Android 的 VPN 设置中关闭该选项。参见[始终开启的 VPN](@/android/permissions.zh.md#always-on)。

### 断开连接后某些应用无法上网 {#fakedns}

`设置`（Settings）→ `DNS` → `启用 FakeDNS`（Enable FakeDNS）会为应用提供只能通过 Throne 使用的虚假地址。Throne 会对此发出警告：“可能导致其他应用程序在代理停止后需要重新启动以重新连接到网络”。请重启这些应用，或关闭 `启用 FakeDNS`。

## Throne 在后台被停止 {#killed-in-background}

典型迹象：屏幕关闭时 VPN 断开、通知消失、解锁手机后连接不再工作，或者通知或小部件上的按钮没有反应。

1. 打开 `设置`（Settings）→ `通用`（General）→ `电池优化`（Battery optimization）。它必须显示“不受限”。如果不是，请点按它，并允许 Throne 不受限制地运行。参见[电池优化](@/android/permissions.zh.md#battery)。
2. 打开 `设置` → `通用` → `后台与自启动`（Background & auto-start）。允许 Throne 自动启动并在后台运行。参见[厂商设置](@/android/permissions.zh.md#oem)。
3. 保持 `当设备从睡眠状态唤醒时重置出站连接`（Reset outbound connections when device wakes from sleep）开启（默认）。设备从深度睡眠中唤醒时，Throne 会关闭旧连接，应用随后重新连接。同时也请保持 `当网络发生变化时重置出站连接`（Reset outbound connections when network changes）开启。自 2.0.5 起，网络在断开后恢复时（例如一段时间没有信号之后），它也会重置连接。
4. 启用始终开启的 VPN，让 Android 自行重新启动 Throne。参见[始终开启的 VPN](@/android/permissions.zh.md#always-on)。
5. 在某些设备上，在最近任务列表中划掉 Throne 会停止它。请将它保留在列表中；如果设备支持，也可以将它锁定在列表中。

## 订阅问题 {#subscriptions}

手动更新订阅时，错误会显示在屏幕底部，格式为分组名称后跟消息。自 2.0.3 起，如果更新没有找到任何配置，会改为打开一个“… 的更新”窗口并列出原因。自动更新只会将错误写入 `日志`（Logs）。

| 消息 | 原因 | 解决方法 |
| --- | --- | --- |
| “请求使用代理但未启动配置。” | `设置`（Settings）→ `订阅`（Subscriptions）中开启了 `使用代理`（Use proxy），而 Throne 未连接。在 2.0.3 及更早版本中，运行模式为 `仅代理`（Proxy only）时也会如此。 | 先连接，或关闭 `使用代理`。在 2.0.3 及更早版本中，关闭它只在 `VPN` 模式下有效。 |
| “传输 … 出错 - 服务器回复：…” | 服务器拒绝了请求，例如返回 403 或 404。 | 检查 URL。许多服务商只接受部分应用：请设置服务商指定的 User-Agent，或开启 HWID 发送。 |
| “订阅中未发现配置；保持不变。” | 返回内容中没有 Throne 能读取的配置，例如返回的是一个网页。 | 通常同样是 User-Agent 或 HWID 的问题。另请检查[导入格式](@/reference/protocols.zh.md#import-formats)。 |
| “服务器返回的是网页而不是订阅。…” | 自 2.0.3 起。服务器发送了一个网页，通常是验证码（CAPTCHA）或机器人检查。 | 在分组的 `高级`（Advanced）设置中，尝试其他 `TLS 版本`（TLS version）、`HTTP 版本`（HTTP version）或 `UserAgent`（User agent）。 |
| “名称过滤排除了该订阅的全部 … 个配置；订阅保持不变。” | 自 2.0.2 起。分组的名称过滤器移除了所有配置。 | 编辑该分组，检查 `过滤`（Filter）下的 `仅保留名称匹配项`（Only names matching）和 `移除名称匹配项`（Remove names matching）。 |
| “不安全重定向” | 服务器将 `https://` URL 重定向到了 `http://`。Throne 不会跟随这类重定向。 | 向服务商索取正确的 `https://` URL。 |
| “重定向过多” | 服务器进行了循环重定向。 | 与服务商核对 URL。 |
| “Response larger than 64 MB”（响应大于 64 MB） | 订阅过大。 | 向服务商索取更小的列表。 |
| “仍在使用，因此保留而非删除”或“已保留正在运行的配置。” | 此次更新或其后的某个清理选项本会移除你正在连接的配置，因此 Throne 保留了它。你会在变更报告或 `日志` 中看到这条消息。 | 无需处理。如果仍要移除它，请在 `设置` → `订阅` 中开启 `允许停止活动配置`（Allow stopping the active profile）。 |

要为单个分组更改 User-Agent 或 HWID，请在侧边菜单中打开 `分组`（Groups），点按该分组的 `编辑`（Edit）按钮，然后打开 `高级`（Advanced）。其中有 `UserAgent`（User agent）和 `发送 HWID`（Send HWID），自 2.0.3 起还有 `TLS 版本`（TLS version）和 `HTTP 版本`（HTTP version）。适用于所有分组的设置位于 `设置`（Settings）→ `订阅`（Subscriptions）。参见 [User-Agent 与 HWID](@/guides/subscriptions.zh.md#user-agent-and-hwid)。

### 服务商只接受来自你自己网络连接的更新 {#direct-updates}

Throne 连接期间，它自己的请求（例如订阅更新）也会经过 Throne，并遵循你的路由配置。有些服务商只接受来自你自己网络连接的更新请求。请添加一条将订阅域名发往 `direct` 的规则：

1. 在侧边菜单中打开 `路由`（Routing）。打开当前路由配置的菜单，选择 `编辑`（Edit）。
2. 选择 `添加规则`（Add rule）。
3. 将 `出站`（Outbound）设为 `direct`。在 `域名后缀`（Domain suffix）中输入订阅 URL 的主机名，例如 `sub.example.com`。
4. 保存该规则和路由配置。把该规则放在匹配同一域名的其他规则之上。

如果服务商要求请求来自其某台服务器，请改为选择该服务器作为 `出站`。

## 路由错误 {#routing}

| 消息 | 原因 | 解决方法 |
| --- | --- | --- |
| “The routing profile is referencing outbounds that no longer exist, consider revising your settings”（路由配置引用了已不存在的出站，请检查你的设置） | 某条规则把流量发往一个已被删除的服务器配置，例如被订阅更新删除。 | 打开该路由配置（`路由`（Routing）→ 其菜单 → `编辑`（Edit））。引用已删除服务器的规则会显示“服务器缺失（#…）”。请选择其他出站。 |
| “Unknown rule-set "…" in rule "…" of routing profile "…"”（路由配置“…”的规则“…”中有未知的规则集“…”） | 某条规则使用了 Throne 不认识的规则集名称。 | 在 `路由` 中点按 `刷新仓库列表`（Refresh repository list），或使用 `.srs` 文件的 URL。 |
| “规则集列表不可用。…” | 规则集名称列表尚未下载。 | 在 `路由` 中点按 `刷新仓库列表`（Refresh repository list）。 |
| “raw routing profiles are not supported on Android”（Android 不支持原始路由配置） | 你导入了来自 Throne 桌面版的原始（raw）路由配置。 | 使用结构化路由配置或远程路由配置。 |

## WARP 错误 {#warp}

| 消息 | 原因 | 解决方法 |
| --- | --- | --- |
| “WARP 已启用但尚未生成配置。请在「设置 › 路由 › WARP」中生成。” | WARP 已开启，但所选模式没有 WARP 配置。 | 打开 `设置`（Settings）→ `路由`（Routing）→ `WARP`，点按 `生成配置`（Generate WARP config）。 |
| “失败: Warp is enabled but its config has not been generated. Please generate the Warp config first in Routing Settings.”（Warp 已启用，但尚未生成其配置。请先在路由设置中生成 Warp 配置。） | 同一问题，在连接时显示。 | 同上。 |
| “生成 WARP 配置失败” | 在 Cloudflare 注册失败。某些网络会阻止注册。 | 连接到一个可用的配置，在 `设置` → `订阅`（Subscriptions）中开启 `使用代理`（Use proxy），然后重新生成。 |
| 没有消息，但开启 WARP 时无法加载任何内容 | 在 `WireGuard` 模式下（以及自 2.0.5 起的 `AmneziaWG` 模式下），WARP 需要一个能转发 UDP 的配置。 | 将 `模式`（Mode）设为 `MASQUE`，将 `HTTP 版本`（HTTP version）设为 `HTTP/2`，这样只需要 TCP。为新模式生成配置。 |

参见 [Cloudflare WARP](@/advanced/warp.zh.md)。

## 更新问题 {#updates}

- “下载使用了与当前构建不同的密钥签名，Android 无法将其作为更新安装。请先备份，卸载当前构建，再从 GitHub 安装发布版。”你的副本不是用 Throne 发布密钥签名的。请创建备份（侧边菜单 → `工具`（Tools）→ `创建备份`（Create backup）），卸载 Throne，安装 GitHub 上的 APK，然后恢复备份。
- “此更新无法在应用内安装（…）。请从发布页下载。”点按 `在浏览器中打开`（Open in browser），下载适用于你设备的 APK，并将其覆盖安装到当前应用之上。
- “无法检查更新”：Throne 无法访问 GitHub。请连接到一个可用的配置后重试。
- Android 不会在旧版 Throne for Android 之上安装 2.0.0。参见[从 1.x 或 NekoBox 升级](@/android/installation.zh.md#upgrading)。

## 配置在桌面版上可用，但在 Android 上不可用 {#desktop-vs-android}

1. 检查配置类型。Tailscale 和额外核心（Extra Core）配置无法在 Android 上工作。TLS 伪装设置在 Android 上会被忽略。
2. 如果订阅中的某个配置在 Android 上缺失：自 2.0.2 起，Throne 不会导入核心在 Android 上无法运行的配置，例如使用了 sing-box 没有的传输方式，或者在 VLESS 以外的配置类型上使用 XHTTP。`日志`（Logs）会列出每个被跳过的配置的原因。
3. 如果该配置是带有 `geoip:` 或 `geosite:` 规则的 Xray 配置，它需要 `geoip.dat` 和 `geosite.dat` 文件。自 2.0.2 起，Throne 会在此类配置首次启动时，从 `设置`（Settings）→ `核心`（Core）→ `Xray Geo 资源`（Xray geo assets）中的 `来源`（Source）下载它们。如果 Throne 提示已安装的文件不包含配置所需的某个类别，请选择包含该类别的来源，例如你的桌面版使用的来源。
4. 比较会影响配置连接方式的设置：`设置` → `预设`（Presets）中的预设（多路复用、TLS 分片、TLS 技巧、uTLS），以及 `设置` → `核心` → `Xray VLESS 偏好`（Xray VLESS preference）。VLESS 首选项在导入配置时生效，因此更改后请重新更新订阅。参见 [sing-box 与 Xray](@/advanced/xray.zh.md#vless-preference)。自 2.0.4 起，还要比较 `设置` → `核心` → `在 Xray 中信任系统证书`（Trust system certificates in Xray）。在 Android 上，它会加入系统的根证书，但不包括你自己安装的证书。
5. 要获得与桌面版相同的设置，请恢复桌面版备份。参见[在桌面版与 Android 之间迁移](@/guides/backup.zh.md#desktop-android)。
6. 如果仍然失败，请附上两个应用的日志报告问题。

## 日志 {#logs}

在侧边菜单中打开 `日志`（Logs）。它显示核心的日志。自 2.0.5 起，对于 Xray 配置，它还会在标有 `xray:` 的行中显示 Xray 的日志。工具栏中有 `更新`（Update），用于重新加载视图，以及 `分享日志`（Share logs）和 `清空日志`（Clear logs）。菜单中有 `保存日志…`（Save logs…）、`隐藏敏感数据`（Hide sensitive data）和 `隐藏目标地址`（Hide destinations）。

自 2.0.3 起，工具栏中还有 `过滤`（Filter）。它只显示包含你输入的文本的行。`区分大小写`（Match case）和 `正则表达式`（Regular expression）会改变文本的比较方式。菜单中还有 `字体大小`（Font size）。向上滚动时，视图会停止跟随新行，`跳到最新`（Jump to latest）可以带你回到末尾。

`分享日志` 和 `保存日志…` 会创建一个文本文件，其中包含：

- 文件头，包括应用和核心的版本、Android 版本、设备型号、ABI、运行模式以及一些不包含机密信息的设置；
- 核心日志，以及上一次连接的核心日志；
- 应用系统日志的最后 2000 行。

`隐藏敏感数据` 默认开启，请保持开启。在导出的文件中，它会替换：

- 每个 URL 的路径和查询部分，以及 URL 中的用户名和密码（主机名保留）；
- 密码、密钥、令牌、UUID 以及类似的值，自 2.0.5 起也包括作为列表或对象的此类值；
- 配置中的服务器地址、SNI 和主机名；
- 公网 IP 地址（私有地址保留）；
- Wi-Fi 名称。

`隐藏目标地址` 还会隐藏连接日志行和 DNS 日志行中的域名。只有在 `隐藏敏感数据` 开启时才能开启它，并且应用重启后 Throne 不会保留该设置。隐藏只是尽力而为：发布文件之前，请先自己读一遍。

如需更详细的信息，请将 `设置`（Settings）→ `核心`（Core）→ `日志级别`（Log level）设为 `info` 或 `debug`，然后点按 `应用`（Apply）重启应用。自 2.0.5 起，对于 Xray 配置，还要在同一界面将 `Xray 日志级别`（Xray log level）设为 `info` 或 `debug`（默认 `warning`）：只有两个级别都允许时，Xray 的日志行才会显示。重现问题后分享日志。之后请将级别分别改回 `warn` 和 `warning`。

崩溃后，Throne 会重新启动，并提示你分享一份已隐藏敏感数据的日志文件。

### 诊断 {#diagnostics}

对于卡死、CPU 占用过高或耗电过快的问题，开发者可能会要求你提供诊断文件（自 2.0.3 起）。它会记录核心在 30 秒内的运行情况。

1. 让 Throne 进入出现问题的状态，例如连接后等待它卡住。
2. 打开 `设置`（Settings）→ `诊断`（Diagnostics）。
3. 开启开发者要求的选项：`锁竞争`（Lock contention）、`执行跟踪（文件较大）`（Execution trace (larger file)）或 `包含 Throne 日志`（Include Throne logs）。包含日志时，请保持 `隐藏敏感数据`（Hide sensitive data）开启。
4. 点按 `开始`（Start）。离开该界面后录制仍会继续，因此你可以在此期间重现问题。`停止`（Stop）会提前结束录制。
5. 点按 `分享`（Share）或 `保存…`（Save…），然后附上文件 `throne-profile-<date>-<time>.zip`。

录制内容不包含任何关于你的配置的信息。它会列出应用和核心的版本、Android 版本、设备型号、ABI 以及少量设置，例如运行模式和正在运行的配置的类型。`包含 Throne 日志` 会加入上文所述的日志文件。桌面版也有相同的功能。参见[诊断](@/help/bug_reports.zh.md#diagnostics)。

## 报告问题 {#report}

{% alert_warning() %}
切勿公开发布订阅 URL、分享链接、备份或配置。任何拿到它们的人都可以使用你的订阅。曾有用户在 issue 中发布了配置，结果不得不重置订阅。
{% end %}

请在 [github.com/throneproj/ThroneForAndroid/issues](https://github.com/throneproj/ThroneForAndroid/issues) 报告 Android 应用的问题。请附上：

- 你做了什么、预期结果是什么，以及实际发生了什么；
- 通过 `分享日志`（Share logs）或 `保存日志…`（Save logs…）生成的日志文件，并开启 `隐藏敏感数据`（Hide sensitive data）；
- 配置类型（例如使用 REALITY 的 VLESS）和运行模式。

日志文件中已经包含应用版本、核心版本和设备型号。一般性建议请参见[报告 Bug](@/help/bug_reports.zh.md)。

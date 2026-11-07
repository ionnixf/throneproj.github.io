+++
title = "Android 故障排除"
description = "解决 Throne for Android 的常见问题。"
weight = 60
toc = true
+++

找到你看到的消息或症状。消息按应用简体中文界面的显示引用；应用中尚未翻译的消息保留英文原文，并在括号中附上中文译文。有关 Throne 桌面版，请参见[故障排除](@/help/troubleshooting.zh.md)。

## VPN 无法启动或没有流量 {#connection}

启动失败时，Throne 会在屏幕底部显示原因，并附带 `日志`（Logs）按钮。

| 消息 | 原因 | 解决方法 |
| --- | --- | --- |
| “VPN 服务权限请求被拒绝” | 你拒绝了 VPN 请求，或者另一个 VPN 应用被设为始终开启的 VPN。 | 重新连接并允许请求。如果没有出现请求，请在 Android 的 VPN 设置中为另一个应用关闭始终开启的 VPN。 |
| “无法请求 VPN 确认” | 设备没有用于 VPN 请求的界面。 | 参见 [VPN 权限](@/android/permissions.zh.md#vpn-permission)。 |
| 包含“configure tun interface”（配置 TUN 接口）的“失败: …” | Android 拒绝创建 VPN 接口。 | 断开其他 VPN 应用，确认它们都没有被设为始终开启的 VPN，然后重启设备。 |
| “失败: Profile … has a type this build cannot use: …”（配置 … 的类型在此版本中无法使用：…） | 该配置类型仅存在于桌面版，例如 Tailscale 或额外核心（Extra Core）。 | 使用其他配置。 |
| “无法连接：网络的 DNS (…) 未应答。…”或“无法连接：系统 DNS 未应答。…” | `直连 DNS`（Direct DNS）没有应答。默认值（`localhost`）使用的是当前网络的 DNS 服务器。 | 点按消息中的 `DNS`，将 `直连 DNS` 设为在此网络上能够应答的服务器，例如 `tls://1.1.1.1`。参见 [Android 上的 DNS](@/guides/dns.zh.md#android)。 |
| “无法下载规则集：…” | 某条规则使用了从未下载过的规则集，而下载失败了。 | 点按 `仍然连接`（Connect anyway），然后点按 `连接`（Connect）。在下载成功之前，这些规则不会匹配任何内容。 |
| “无法从 … 下载 geoip.dat：…”（或 `geosite.dat`） | 带有 `geoip:` 或 `geosite:` 规则的 Xray 配置需要该文件。 | 点按 `Geo 资源`（Geo assets），使用其他配置连接后点按 `立即下载`（Download now），或选择其他 `来源`（Source）。 |

对于其他任何“失败: …”消息，请点按 `日志`（Logs）并阅读最后几行。

### 已连接，但无法加载任何内容 {#no-traffic}

1. 点按屏幕底部的横栏。“HTTP：… 毫秒”表示该配置可用；“失败: …”表示该配置或其服务器不可用。
2. 如果测试失败，请使用 ⋮ → `测试`（Test）→ `URL 测试`（URL test），然后选择一个可用的配置。参见[测试与自动选择器](@/guides/testing.zh.md)。
3. 如果 `设置`（Settings）→ `TUN / VPN` → `分应用代理`（Apps VPN mode）已开启，请将其关闭后再次测试。参见[分应用代理](@/android/modes.zh.md#per-app-proxy)。
4. 将 `设置` → `路由`（Routing）→ `当前路由配置`（Current routing profile）设为 `Default`，然后再次测试。如果有效，问题出在你的路由配置中的某条规则。如果 Throne 提示该分组使用自己的路由配置，请改为在分组设置中将 `路由配置`（Routing profile）设为 `默认（…）`（Default (…)）。
5. 在 `设置` → `DNS` 中尝试其他 `远程 DNS`（Remote DNS）；如果路由配置使用自己的 DNS 服务器，则在该路由配置中修改。参见 [DNS](@/guides/dns.zh.md)。
6. 如果你在 `设置` → `TUN / VPN` 中开启了 `Tun IPv6` 或 `Tun 路由`（Tun routing），请将其关闭。如果某些应用或网站无法访问而其他的正常，请在同一界面中将 `MTU` 设为 `1500` 并重新连接。
7. 要确定问题是否由 VPN 模式引起，请将 `设置` → `通用`（General）→ `运行模式`（Service mode）设为 `仅代理`（Proxy only）并连接，然后在一个应用自带的代理设置中填写 SOCKS5 代理 `127.0.0.1`、端口 `2080`。如果该应用可以正常工作，请再检查一遍第 3 步和第 6 步。

### Throne 关闭时无法上网 {#blocked-without-vpn}

- Android 的“始终开启的 VPN”与“屏蔽未使用 VPN 的所有连接”（Block connections without VPN）同时开启时，只要 Throne 未连接，Android 就会阻止所有流量。请连接 Throne，或在 Android 的 VPN 设置中关闭该选项。参见[始终开启的 VPN](@/android/permissions.zh.md#always-on)。
- 开启 `设置` → `DNS` → `启用 FakeDNS`（Enable FakeDNS）时，Throne 停止后某些应用会失去连接。请重启这些应用，或关闭 `启用 FakeDNS`。

## Throne 在后台被停止 {#killed-in-background}

迹象：屏幕关闭时 VPN 断开、解锁手机后连接不再工作，或者通知消失。

1. `设置`（Settings）→ `通用`（General）→ `电池优化`（Battery optimization）必须显示“不受限”。如果不是，请点按它。参见[电池优化](@/android/permissions.zh.md#battery)。
2. 在 `设置` → `通用` → `后台与自启动`（Background & auto-start）中，允许 Throne 自动启动并在后台运行。参见[厂商设置](@/android/permissions.zh.md#oem)。
3. 在 `设置` → `通用` 中保持 `当设备从睡眠状态唤醒时重置出站连接`（Reset outbound connections when device wakes from sleep）和 `当网络发生变化时重置出站连接`（Reset outbound connections when network changes）开启（默认），这样应用在深度睡眠或网络中断后会重新连接。
4. 启用始终开启的 VPN，让 Android 自行重新启动 Throne。参见[始终开启的 VPN](@/android/permissions.zh.md#always-on)。
5. 在某些设备上，在最近任务列表中划掉 Throne 会停止它。请将它保留在列表中，或将它锁定在列表中。

## 订阅问题 {#subscriptions}

手动更新的错误会显示在屏幕底部。自动更新只会将错误写入 `日志`（Logs）。

| 消息 | 原因 | 解决方法 |
| --- | --- | --- |
| “请求使用代理但未启动配置。” | 开启了 `设置`（Settings）→ `订阅`（Subscriptions）→ `使用代理`（Use proxy），而 Throne 未连接。 | 先连接，或关闭 `使用代理`。 |
| “传输 … 出错 - 服务器回复：…” | 服务器拒绝了请求，例如返回 403 或 404。 | 检查 URL。许多服务商只接受部分应用：请设置服务商指定的 User-Agent，或开启 HWID 发送。 |
| “订阅中未发现配置；保持不变。” | 返回内容中没有 Throne 能读取的配置。 | 通常同样是 User-Agent 或 HWID 的问题。另请检查[导入格式](@/reference/protocols.zh.md#import-formats)。 |
| “服务器返回的是网页而不是订阅。…” | 通常是验证码（CAPTCHA）或机器人检查。 | 在分组的 `高级`（Advanced）设置中，尝试其他 `TLS 版本`（TLS version）、`HTTP 版本`（HTTP version）或 `UserAgent`（User agent）。 |
| “名称过滤排除了该订阅的全部 … 个配置；订阅保持不变。” | 分组的名称过滤器移除了所有配置。 | 在分组设置的 `过滤`（Filter）下检查 `仅保留名称匹配项`（Only names matching）和 `移除名称匹配项`（Remove names matching）。 |
| “不安全重定向”、“重定向过多”或“响应超过 64 MB” | 服务器将 `https://` 重定向到了 `http://`（Throne 不会跟随这类重定向），进行了循环重定向，或者发送的内容超过 64 MB。 | 与服务商核对 URL。 |

User-Agent 和 HWID 位于 `设置` → `订阅`；单个分组的设置位于 `分组`（Groups）→ 该分组的 `编辑`（Edit）按钮 → `高级`。参见 [User-Agent 与 HWID](@/guides/subscriptions.zh.md#user-agent-and-hwid)。

### 服务商只接受来自你自己网络连接的更新 {#direct-updates}

Throne 连接期间，它自己的请求（例如订阅更新）也会遵循你的路由配置。如果服务商只接受来自你自己网络连接的更新，请添加一条将订阅域名发往 `direct` 的规则：

1. 在侧边菜单中打开 `路由`（Routing），然后打开当前路由配置的菜单 → `编辑`（Edit）→ `添加规则`（Add rule）。
2. 将 `出站`（Outbound）设为 `direct`，在 `域名后缀`（Domain suffix）中输入订阅 URL 的主机名（例如 `sub.example.com`），然后保存该规则和路由配置。该规则必须位于匹配同一域名的其他规则之上。

如果服务商要求请求来自其某台服务器，请改为选择该服务器作为 `出站`。

## 路由错误 {#routing}

| 消息 | 原因 | 解决方法 |
| --- | --- | --- |
| “The routing profile is referencing outbounds that no longer exist, consider revising your settings”（路由配置引用了已不存在的出站，请检查你的设置） | 某条规则把流量发往一个已被删除的服务器配置，例如在订阅更新之后。 | 编辑该路由配置：引用已删除服务器的规则会显示“服务器缺失（#…）”。请选择其他出站。 |
| “Unknown rule-set "…" in rule "…" of routing profile "…"”（路由配置“…”的规则“…”中有未知的规则集“…”）或“规则集列表不可用。…” | 某条规则使用了 Throne 不认识的规则集名称，或者规则集名称列表尚未下载。 | 在 `路由` 中点按 `刷新仓库列表`（Refresh repository list），或使用 `.srs` 文件的 URL。 |
| “raw routing profiles are not supported on Android”（Android 不支持原始路由配置） | 你导入了来自 Throne 桌面版的原始（raw）路由配置。 | 使用结构化路由配置或远程路由配置。 |

## WARP 错误 {#warp}

| 消息 | 原因 | 解决方法 |
| --- | --- | --- |
| “WARP 已启用但尚未生成配置。…”，连接时为“失败: Warp is enabled but …” | 所选模式没有 WARP 配置。 | 在 `设置`（Settings）→ `路由`（Routing）→ `WARP` 中点按 `生成配置`（Generate WARP config）。 |
| “生成 WARP 配置失败” | 在 Cloudflare 注册失败。某些网络会阻止注册。 | 连接到一个可用的配置，开启 `设置` → `订阅`（Subscriptions）→ `使用代理`（Use proxy），然后重新生成。 |
| 没有消息，但开启 WARP 时无法加载任何内容 | 在 `WireGuard` 和 `AmneziaWG` 模式下，WARP 需要一个能转发 UDP 的配置。 | 将 `模式`（Mode）设为 `MASQUE`，将 `HTTP 版本`（HTTP version）设为 `HTTP/2`，这样只需要 TCP，然后为它生成配置。 |

参见 [Cloudflare WARP](@/advanced/warp.zh.md)。

## 更新问题 {#updates}

- “下载使用了与当前构建不同的密钥签名，…”：你的副本不是用 Throne 发布密钥签名的。请创建备份（侧边菜单 → `工具`（Tools）→ `创建备份`（Create backup）），卸载 Throne，安装 GitHub 上的 APK，然后恢复备份。
- “此更新无法在应用内安装（…）。…”：点按 `在浏览器中打开`（Open in browser），下载适用于你设备的 APK，并将其覆盖安装到当前应用之上。
- “无法检查更新”：Throne 无法访问 GitHub。请连接到一个可用的配置后重试。
- Android 无法在 1.x 之上安装 Throne for Android 2.0 或更高版本。参见[从 1.x 或 NekoBox 升级](@/android/installation.zh.md#upgrading)。

## 配置在桌面版上可用，但在 Android 上不可用 {#desktop-vs-android}

1. Tailscale 和额外核心（Extra Core）配置无法在 Android 上工作，TLS 伪装设置也会被忽略。
2. Throne for Android 不会导入其核心无法运行的订阅配置，例如使用了 sing-box 没有的传输方式，或者在 VLESS 以外的配置类型上使用 XHTTP。`日志`（Logs）会列出每个配置被跳过的原因。
3. 带有 `geoip:` 或 `geosite:` 规则的 Xray 配置需要 `geoip.dat` 和 `geosite.dat`，Throne 会从 `设置`（Settings）→ `核心`（Core）→ `Xray Geo 资源`（Xray geo assets）中的 `来源`（Source）下载它们。如果已安装的文件不包含配置所需的某个类别，请选择包含该类别的来源，例如你的桌面版使用的来源。
4. 比较 `设置` → `预设`（Presets），以及 `设置` → `核心` 中的 `Xray VLESS 偏好`（Xray VLESS preference）和 `在 Xray 中信任系统证书`（Trust system certificates in Xray）。VLESS 偏好在导入时生效，因此更改后请重新更新订阅。在 Android 上，信任系统证书不包括你自己安装的证书。
5. 要获得与桌面版相同的设置，请恢复桌面版备份。参见[在桌面版与 Android 之间迁移](@/guides/backup.zh.md#desktop-android)。

## 日志 {#logs}

在侧边菜单中打开 `日志`（Logs）。它显示核心的日志；对于 Xray 配置，还会在标有 `xray:` 的行中显示 Xray 的日志。

`分享日志`（Share logs）和 `保存日志…`（Save logs…）会创建一个文本文件，其中包含版本信息、设备型号、本次和上一次连接的核心日志，以及应用的系统日志。

菜单中的 `隐藏敏感数据`（Hide sensitive data）默认开启，请保持开启。它会在文件中隐藏 URL 路径、凭据、密钥、服务器地址、主机名、公网 IP 地址和 Wi-Fi 名称。`隐藏目标地址`（Hide destinations）还会隐藏连接日志行和 DNS 日志行中的域名。隐藏只是尽力而为：发布文件之前，请先自己读一遍。

如需更详细的信息，请将 `设置`（Settings）→ `核心`（Core）→ `日志级别`（Log level）设为 `info` 或 `debug`，然后点按 `应用`（Apply）重启应用。对于 Xray 配置，还要在同一界面设置 `Xray 日志级别`（Xray log level）：只有两个级别都允许时，Xray 的日志行才会显示。重现问题并分享日志，之后请将级别分别改回 `warn` 和 `warning`。

### 诊断 {#diagnostics}

对于卡死、CPU 占用过高或耗电过快的问题，开发者可能会要求你提供诊断文件。它会记录核心在 30 秒内的运行情况，不包含你的配置中的任何内容。

1. 让 Throne 进入出现问题的状态。
2. 打开 `设置`（Settings）→ `诊断`（Diagnostics），开启开发者要求的选项：`锁竞争`（Lock contention）、`执行跟踪（文件较大）`（Execution trace (larger file)）或 `包含 Throne 日志`（Include Throne logs）。包含日志时，请保持 `隐藏敏感数据`（Hide sensitive data）开启。
3. 点按 `开始`（Start）。离开该界面后录制仍会继续，因此你可以在此期间重现问题。
4. 点按 `分享`（Share）或 `保存…`（Save…），然后附上文件 `throne-profile-<date>-<time>.zip`。

## 报告问题 {#report}

{% alert_warning() %}
切勿公开发布订阅 URL、分享链接、备份或配置。任何拿到它们的人都可以使用你的订阅。
{% end %}

请在 [github.com/throneproj/ThroneForAndroid/issues](https://github.com/throneproj/ThroneForAndroid/issues) 报告问题，并写明：你做了什么、预期结果是什么、实际发生了什么；附上开启 `隐藏敏感数据`（Hide sensitive data）后通过 `分享日志`（Share logs）生成的文件；以及配置类型（例如使用 REALITY 的 VLESS）和运行模式。另请参见[报告 Bug](@/help/bug_reports.zh.md)。

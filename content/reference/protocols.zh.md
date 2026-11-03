+++
title = "协议与导入格式"
description = "Throne 桌面版和 Android 版支持的配置档类型、可以导入的链接和文件，以及如何以链接或二维码的形式分享配置档。"
weight = 10
toc = true
+++

本页列出了 Throne 桌面版和 Throne for Android 中的所有配置档类型、Throne 可以导入的链接和文件，以及分享配置档的各种方式。导入服务器之前，可以用本页确认 Throne 是否支持它。

## 配置档类型 {#protocols}

要手动创建配置档，请在桌面版中使用 `程序`（Program）→ `新建配置档`（New profile，快捷键 `Ctrl+N`），或在 Android 上使用 `添加服务器配置`（Add profile）→ `手动输入`（Manual settings），然后选择类型。已有配置档的类型无法更改。在简洁模式下，桌面版的类型列表不提供 `直连`（Direct）、`链式代理`（Chain Proxy）、`额外核心`（Extra Core）和各种 `自定义`（Custom）类型（仅在高级模式下显示，参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）。

| 类型 | 桌面版 | Android | 说明 |
|---|---|---|---|
| `自动选择器`（Auto Selector） | 支持 | 支持 | 从一个分组中挑选最佳的可用配置档，并自动切换。参见[自动选择器](@/guides/testing.zh.md#auto-selector)。 |
| `Socks` | 支持 | 支持 | SOCKS 版本 5 或 4。 |
| `HTTP` | 支持 | 支持 | HTTP 代理，可选择通过 TLS（HTTPS）传输。 |
| `Shadowsocks` | 支持 | 支持 | 包括 2022 系列加密方式，以及 `obfs-local` 和 `v2ray-plugin` 插件。 |
| `VMess` | 支持 | 支持 | |
| `Trojan` | 支持 | 支持 | |
| `VLESS` | 支持 | 支持 | sing-box 实现，支持 REALITY。 |
| `VLESS (Xray)` | 支持 | 支持 | Xray 实现：支持 XHTTP、REALITY、VLESS 加密、Finalmask、[XDRIVE](@/advanced/xray.zh.md#xdrive) 传输（桌面版自 1.4.0 起，Android 版自 2.0.4 起），以及带 [XDNS](@/advanced/dns_tunnels.zh.md#xdns) 的 mKCP 传输（桌面版自 1.4.0 起）。参见 [sing-box 与 Xray](@/advanced/xray.zh.md#vless-preference)。 |
| `Hysteria` | 支持 | 支持 | Hysteria 1 和 Hysteria 2 合为一种类型：通过 `协议版本`（Protocol Version）选择。支持端口跳跃和混淆。自 1.4.0 起（Android：自 2.0.5 起），当 `上行 (Mbps)`（Up Mbps）或 `下行 (Mbps)`（Down Mbps）留空时，Hysteria 1 使用上行 10 Mbps、下行 50 Mbps。为获得最佳效果，请填写你线路的真实速度。 |
| `TUIC` | 支持 | 支持 | |
| `Juicity` | 支持 | 支持 | |
| `Naive` | 支持 | 支持 | 基于 HTTPS 或 QUIC 的 NaïveProxy。Android 上为 `Naïve`。 |
| `TrustTunnel` | 支持 | 支持 | |
| `AnyTLS` | 支持 | 支持 | |
| `Mieru` | 支持 | 支持 | |
| `Snell` | 支持 | 支持 | Snell 版本 4 或 6。 |
| `ShadowTLS` | 支持 | 支持 | |
| `WireGuard` | 支持 | 支持 | 如需使用 AmneziaWG，请勾选 `启用 Amnezia`（Enable Amnezia；Android 上为 `启用 AmneziaWG`（Enable AmneziaWG））。自 1.4.0 起（Android：自 2.0.3 起），`Amnezia 选项`（Amnezia Options）→ `Generate QUIC Signature...`（Android 上为 `生成 QUIC 签名…`（Generate QUIC signature…））会填写签名包 I1–I5，使握手看起来像是到你所选域名的 HTTP/3 连接。可以创建 Cloudflare WARP 账户，参见 [WARP](@/advanced/warp.zh.md#generate)。 |
| `MASQUE` | 支持 | 支持 | HTTP/3 或 HTTP/2。可以创建 Cloudflare WARP 身份。 |
| `OpenVPN` | 支持 | 支持 | 可导入 `.ovpn` 文件。一次性密码（OTP）仅在桌面版上可用。参见 [OpenVPN](@/advanced/vpn_profiles.zh.md#openvpn)。 |
| `OpenConnect` | 支持 | 支持 | 支持 AnyConnect、GlobalProtect、Fortinet、F5、Pulse 和 Juniper（`nc`）服务器。OTP 仅在桌面版上可用。参见 [OpenConnect](@/advanced/vpn_profiles.zh.md#openconnect)。 |
| `Tailscale` | 支持 | 不支持 | 加入你的 Tailscale 网络，并可使用出口节点。参见 [Tailscale](@/advanced/vpn_profiles.zh.md#tailscale)。 |
| `SSH` | 支持 | 支持 | |
| `DNSTT / VayDNS` | 支持 | 不支持 | 连接 dnstt 或 VayDNS 服务器的 DNS 隧道，自 1.4.0 起。参见 [DNSTT 和 VayDNS](@/advanced/dns_tunnels.zh.md#dnstt)。 |
| `MasterDNS` | 支持 | 不支持 | 连接 MasterDnsVPN 或 StormDNS 服务器的 DNS 隧道，自 1.4.0 起。参见 [MasterDNS](@/advanced/dns_tunnels.zh.md#masterdns)。 |
| `直连`（Direct） | 支持 | 支持 | 不使用代理。在配置档中设置绑定接口，即可让流量通过该网卡发出。 |
| `自定义 (sing-box 出站)`（Custom (sing-box outbound)）、`自定义 (sing-box 配置)`（Custom (sing-box config)） | 支持 | 支持 | 你自己的 sing-box JSON：单个出站，或原样运行的完整配置。Android 上为 `自定义配置`（Custom config）。参见[自定义配置](@/advanced/chains.zh.md#custom-config)。 |
| `自定义 (Xray 出站)`（Custom (Xray outbound)）、`自定义 (Xray 配置)`（Custom (Xray config)） | 支持 | 支持 | 同上，用于 Xray JSON。 |
| `额外核心`（Extra Core） | 支持 | 不支持 | 运行你选择的外部程序，并通过它的本地 SOCKS 端口与之连接。参见[额外核心](@/advanced/chains.zh.md#extra-core)。 |
| `链式代理`（Chain Proxy） | 支持 | 支持 | 让流量依次经过多个配置档。Android 上为 `链式代理`（Proxy chain）。参见[代理链](@/advanced/chains.zh.md#chains)。 |

部分 Android 标签只是大小写不同，例如 `SOCKS` 和 `Auto selector`。

`AnyTLS`、`Hysteria`、`Juicity`、`MASQUE`、`ShadowTLS`、`TrustTunnel` 和 `TUIC` 始终使用 TLS，因此它们的编辑器中没有关闭 TLS 的开关。在 2.0.3 及更早版本的 Throne for Android 中，手动创建的这类配置档在保存时会丢失其 TLS 设置（例如 SNI）。自 2.0.4 起，编辑器会保留这些设置。

### 由哪个引擎运行配置档 {#engines}

Throne 的核心 ThroneCore 使用 sing-box 运行所有类型的配置档，但有两个例外：

- `VLESS (Xray)`、`自定义 (Xray 出站)`（Custom (Xray outbound)）和 `自定义 (Xray 配置)`（Custom (Xray config)）在核心内置的 Xray 引擎中运行。
- `额外核心`（Extra Core）运行你选择的外部程序。

DNS 隧道类型 `DNSTT / VayDNS` 和 `MasterDNS` 在 sing-box 中运行。XDNS 是 `VLESS (Xray)` 配置档，因此在 Xray 中运行。参见 [DNS 隧道](@/advanced/dns_tunnels.zh.md#how-it-works)。

Throne for Android 使用相同的核心，因此同样适用这些规则。代理链可以混用两种引擎，但顺序只能是：先是 sing-box 跳点，然后是 Xray 跳点，最后又是 sing-box 跳点。

导入 `vless://` 链接时，由 `设置`（Settings）→ `基本设置`（Basic Settings）→ `核心`（Core）→ `Xray VLESS 首选项`（Xray VLESS Preference，仅在高级模式下显示）决定它成为哪种类型。在默认值 `XHTTP And Reality` 下，使用 XHTTP 或 REALITY 的链接会成为 `VLESS (Xray)`，其他链接则成为 `VLESS`。需要仅 Xray 才有的功能（例如 VLESS 加密，或自 1.4.0 起的 mKCP 传输 `type=kcp`）的链接，总是成为 `VLESS (Xray)`。自 1.4.0 起，Clash YAML 中使用 REALITY 的 VLESS 条目也遵循这一设置。参见 [sing-box 与 Xray](@/advanced/xray.zh.md#vless-preference)。

## 导入格式 {#import-formats}

### 从哪里导入 {#where-to-import}

| 来源 | 桌面版 | Android |
|---|---|---|
| 剪贴板 | `Ctrl+V`，或 `程序` → `添加剪贴板中的配置档`（Add profile from clipboard） | `添加服务器配置` → `从剪切板导入`（Import from clipboard） |
| 文件 | `程序` → `添加文件中的配置档`（Add profile from File(s)，快捷键 `Ctrl+O`），或将文件拖到主窗口上 | `添加服务器配置` → `从文件中导入`（Import from file） |
| 二维码 | `程序` → `扫描二维码`（Scan QR Code，快捷键 `Ctrl+Shift+Q`）可读取屏幕上显示的二维码。二维码图片也可以作为文件导入。 | `添加服务器配置` → `扫描二维码`（Scan QR code）：使用相机，或点按 `从相册选择图片`（Select image） |
| 订阅 | 类型为 `订阅`（Subscription）的分组，参见[订阅与分组](@/guides/subscriptions.zh.md#add-subscription) | 同上，位于 `分组`（Groups）中 |
| 链接 | `throne://` 链接，参见[深度链接](@/advanced/deeplinks.zh.md) | 在其他应用中打开的 `throne://` 链接和协议链接 |

导入的配置档会放入当前分组。如果剪贴板中只有一个 `http://` 或 `https://` URL，Throne 会询问如何使用它：作为新的订阅分组、一次性导入到当前分组，还是作为 HTTP 代理。

### 支持的格式 {#formats}

Throne 桌面版和 Throne for Android 在粘贴的文本、文件、二维码和订阅响应中识别相同的格式。

| 格式 | 结果 |
|---|---|
| 分享链接，每行一个：`vless://`、`vmess://`、`ss://`、`trojan://`、`socks://`、`socks4://`、`socks4a://`、`socks5://`、`http://`、`https://`、`hysteria://`、`hysteria2://`、`hy2://`、`tuic://`、`juicity://`、`anytls://`、`mieru://`、`mierus://`、`snell://`、`tt://`（TrustTunnel）、`shadowtls://`、`wg://`、`wireguard://`、`awg://`（桌面版，自 1.4.0 起）、`ssh://`、`naive+https://`、`naive+quic://` | 每个链接生成一个配置档 |
| Throne 链接：`throne://add/…` 以及较旧的 `json://…` | 一个任意类型的配置档 |
| AmneziaVPN 链接：`vpn://…` | 链接中包含的各个配置 |
| DNS 隧道链接：`dnst://`、`slipnet://`、`masterdns://`、`stormdns://`（桌面版，自 1.4.0 起） | 每个链接生成一个 `DNSTT / VayDNS` 或 `MasterDNS` 配置档，参见 [DNS 隧道](@/advanced/dns_tunnels.zh.md#import) |
| sing-box JSON：完整配置，或出站数组 | 每个受支持的出站生成一个配置档，桌面版还包括 `dnstt` 和 `masterdns`（自 1.4.0 起） |
| sing-box JSON：单个出站对象 | 一个 `自定义 (sing-box 出站)` 配置档 |
| Xray JSON：配置、出站数组或单个出站 | VLESS 出站生成 `VLESS (Xray)`，其他出站生成 `自定义 (Xray 出站)` |
| Xray JSON：完整配置的数组 | 每个配置生成一个 `自定义 (Xray 配置)` 配置档 |
| SIP008 JSON（Shadowsocks） | `Shadowsocks` 配置档 |
| 含有 `proxies:` 列表的 Clash 或 Mihomo YAML | 为 `socks5`、`http`、`ss`、`vmess`、`vless`、`trojan`、`anytls`、`snell`、`hysteria`、`hysteria2`、`tuic`、`masque` 和 `ssh` 条目生成配置档 |
| WireGuard 或 AmneziaWG 的 `.conf` 文件 | 一个 `WireGuard` 配置档，以 `# Name =` 或 `# remarks:` 注释命名，或者以 `[Peer]` 上方的注释命名（桌面版自 1.4.0 起，Android 自 2.0.2 起） |
| OpenVPN 的 `.ovpn` 文件 | 一个 `OpenVPN` 配置档 |
| AnyConnect XML 配置文件、`openconnect` 命令行，或含有 `protocol=` 行的文件 | `OpenConnect` 配置档 |
| 以上任意格式经 Base64 编码后的内容 | 先解码，再导入 |

`tt://` 包括两种 TrustTunnel 链接：`tt://user:password@server:port?…`，以及 TrustTunnel 应用生成的 `tt://?…` 深度链接。Throne 可以读取第 1 版深度链接，自 1.4.0 起（Android：自 2.0.4 起）也可以读取第 2 版。链接中必须包含服务器地址、服务器名称、用户名和密码，因此只带有订阅地址的链接会被拒绝。如果链接列出了多个服务器地址，Throne 会导入第一个，并对其余地址给出警告。

不是代理服务器的条目会被跳过，例如 sing-box 的 `direct`、`block`、`selector` 和 `urltest` 出站，或 Xray 的 `freedom` 和 `blackhole` 出站。要运行完整的 sing-box 或 Xray 配置，请创建 `自定义 (sing-box 配置)`（Custom (sing-box config)）或 `自定义 (Xray 配置)`（Custom (Xray config)）配置档，并把配置粘贴进去。参见[完整配置](@/advanced/chains.zh.md#full-configs)。

两个应用都会拒绝大于 50 MB 的导入文件（在 Android 上，`.zip` 文件不受此限制），以及大于 64 MB 的订阅响应。

### 无法使用的值 {#unusable-values}

自 1.4.0 起（Android：自 2.0.5 起），一些 Throne 无法使用的值不再导致配置档导入失败。Throne 会略去该值，导入其余部分，并在[变更报告](@/guides/subscriptions.zh.md#change-report)中给出警告：

- `VLESS` 或 `VLESS (Xray)` 配置档无法使用的 `flow`，例如用在不支持 Vision 的传输上的 Vision。
- 无效的 XHTTP 设置。无效的 XHTTP 模式会变为 `auto`。
- 无效的 REALITY `spx`（spider X）。
- 无效的 Mieru 端口或端口范围。

在相同版本中，Throne 还能读取以下值：

- Hysteria 链接中带单位的 `upmbps` 和 `downmbps`，例如 `55Mbps`。
- 用逗号分隔的多个 WireGuard `local_address` 条目。
- `VLESS (Xray)` 链接中为空的 `encryption`，表示 `none`。

自 1.4.0 起，混淆类型为 `none`、`plain` 或为空的 Hysteria 2 链接、JSON 或 Clash 条目会以不混淆的方式导入。在编辑器中，将 `混淆`（Obfuscation）设为 `无`（None）可达到同样效果。

### Android 上的差异 {#android-import}

- 订阅 URL 也可以是指向设备上某个文件的 `content://` 地址。
- `从文件中导入`（Import from file）也接受 `.zip` 文件，并会导入其中的每个文件，例如多个 WireGuard 或 OpenVPN 配置。
- 以 `clash://install-config?url=…` 开头的链接会打开 Throne，并将该 URL 添加为订阅。
- 在其他应用中点按协议链接（例如 `vless://…`）或 `throne://` 链接时，Throne 会打开，并在导入配置档之前先征求你的确认。
- Android 的分享面板只会为二进制文件（`application/octet-stream`）提供 Throne 选项，例如备份文件。要从聊天应用导入链接，请复制该链接并使用 `从剪切板导入`（Import from clipboard）。其他文件请使用 `从文件中导入`（Import from file）。
- `Tailscale`、`额外核心`（Extra Core）、`DNSTT / VayDNS` 和 `MasterDNS` 配置档无法在 Android 上使用，Android 版也不导入 DNS 隧道链接。
- 自 2.0.2 起，Throne for Android 在导入和更新时会跳过其核心无法运行的条目，例如非 VLESS 类型配置档上的 XHTTP。日志会把每个这样的条目标为 `Skipped`，并注明类型、名称和原因。

## 分享链接 {#share-links}

### 链接类型 {#link-types}

| 链接 | 形式 | 谁能识别 |
|---|---|---|
| 标准链接 | `vless://…`、`ss://…`、`hysteria2://…` 等 | Throne 和大多数其他客户端 |
| Throne 链接 | `throne://add/…` | Throne 桌面版和 Throne for Android |
| 旧版 Throne 链接 | `json://…` | Throne，仅可导入 |

**标准链接**使用该协议通常的链接格式。有些类型没有标准链接：`自动选择器`（Auto Selector）、`链式代理`（Chain Proxy）、各种 `自定义`（Custom）类型、`直连`（Direct）、`额外核心`（Extra Core）、`MASQUE`、`OpenVPN` 和 `OpenConnect`。使用 XDRIVE 传输的 `VLESS (Xray)` 配置档也没有标准链接。对于这些类型，Throne 会改为提供 Throne 链接。如果这类配置档设置了高级连接选项（例如绑定接口），Throne 显示的将不是 Throne 链接，而是一条只包含这些选项的不完整链接。对于这些配置档，请勾选 `Deep Link`，或使用 `复制选定项的链接 (深度链接)`（Copy links of selected (Deep Links)）。对于 `Tailscale`，也请分享 Throne 链接：Throne 无法导入它为这类配置档显示的 `ts://` 链接。`DNSTT / VayDNS` 和 `MasterDNS` 配置档会生成 `dnst://` 链接，参见 [DNS 隧道](@/advanced/dns_tunnels.zh.md#import)。

**Throne 链接**以 Base64 编码的 JSON 保存配置档的设置。它适用于所有配置档类型，包括没有标准链接的类型，但只有 Throne 能识别。参见[深度链接](@/advanced/deeplinks.zh.md#add)。`json://` 链接是 1.2.0 之前使用的 Throne 链接格式，Throne 仍然可以导入。

`链式代理`（Chain Proxy）或 `自动选择器`（Auto Selector）的链接只保存其所用配置档或分组的内部编号，而不保存服务器本身。因此它只在同一个 Throne 安装中有用。

### 链接中的 ECH 与证书固定 {#ech-links}

自桌面版 1.4.0 和 Android 版 2.0.2 起，Throne 会在标准链接中读写以下 TLS 参数：

| 参数 | 含义 |
|---|---|
| `ech=1` 或 `ech=true` | 开启 ECH。核心会在 DNS 中查找 ECH 配置。 |
| `ech=` 一个域名，或域名加 `+` 再加 DNS 服务器 URL | 开启 ECH，并指定要查询的域名，以及可选的要询问的 DNS 服务器，例如 `cloudflare-ech.com+https://1.1.1.1/dns-query`。它会填入 `ECH Query Name / Resolver`。 |
| `ech=` 其他任意值 | 使用这段 Base64 编码的 ECH 配置开启 ECH。 |
| `pcs=` | 服务器证书的 SHA-256 哈希，十六进制，以逗号分隔。匹配的证书会代替通常的 CA 校验被接受。它会填入 `证书 SHA256`（Certificate SHA256）。 |

对于 `VLESS (Xray)` 配置档，`ech` 的值会原样填入 `ECH Config List`。Throne 生成的链接也会保留旧的 `ech_enabled`、`ech_config` 和 `ech_server_name` 参数。关于 ECH 的作用，请参见 [ECH](@/advanced/presets.zh.md#ech)。

### 在桌面版上分享 {#share-desktop}

在列表中右键点击一个配置档，然后打开 `分享`（Share）：

| 操作 | 得到的内容 |
|---|---|
| `二维码和链接`（QR Code and link） | 一个显示标准链接及其二维码的窗口。勾选 `Deep Link` 可改为显示 Throne 链接。没有标准链接的类型打开时通常已勾选 `Deep Link`（见上文）。 |
| `复制选定项的链接`（Copy links of selected，快捷键 `Ctrl+C`） | 所有选中配置档的标准链接，每行一个。没有标准链接的类型通常会复制为 Throne 链接（见上文）。 |
| `复制选定项的链接 (深度链接)`（Copy links of selected (Deep Links)，快捷键 `Ctrl+Alt+C`） | 所有选中配置档的 Throne 链接。 |

要复制整个分组的链接，请打开 `分组`（Groups）→ `编辑当前分组`（Edit current Group），然后点击 `复制配置档分享链接`（Copy profile share links）或 `复制配置档分享链接(深度链接)`（Copy profile share links (Deep Links)）。

`分享`（Share）→ `导出配置`（Export config，`Ctrl+E`）会显示 Throne 为该配置档生成的核心配置，包含一个 `sing-box` 标签页；对于使用 Xray 的配置档，还有一个 `Xray` 标签页。`复制`（Copy）会复制当前显示的标签页。这是供其他客户端使用的配置文件，而不是分享链接。在 1.4.0 之前，这里是 `导出 Sing-box 配置`（Export Sing-box config）和 `导出 Xray 配置`（Export Xray config）。

在简洁模式下，`复制选定项的链接 (深度链接)` 和 `导出配置` 会被隐藏（仅在高级模式下显示，参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）。

要导入屏幕上显示的二维码，请使用 `程序`（Program）→ `扫描二维码`（Scan QR Code，快捷键 `Ctrl+Shift+Q`）。要从图片文件导入二维码，请把文件拖放到主窗口上，或通过 `程序`（Program）→ `添加文件中的配置档`（Add profile from File(s)）打开它。

### 在 Android 上分享 {#share-android}

点按配置档的分享按钮。双列卡片以及自 2.0.5 起的 `紧凑配置行`（Compact profile rows）没有分享按钮：请改为点按该行的 ⋮ → `分享`（Share）。自 2.0.5 起，`双列`（Double column）布局只能在横屏时通过工具栏按钮使用。参见[配置列表布局](@/android/_index.zh.md#layout)。

菜单提供：

- `二维码`（QR code）→ `标准`（Standard）或 `Throne 链接`（Throne link）
- `导出到剪切板`（Export to clipboard）→ `标准` 或 `Throne 链接`
- `配置`（Configuration）→ `导出到剪切板` 或 `导出到文件`（Export to file）：Throne 生成的核心配置，而不是分享链接

如果配置档类型没有标准链接，就不会出现 `标准`（Standard）。在 Android 上无法分享代理链。要复制整个分组的链接，请打开 `分组`（Groups），点按该分组上的 `更多选项`（More options），然后选择 `复制配置分享链接`（Copy profile share links）或 `复制配置分享链接（深度链接）`（Copy profile share links (deep links)）。

要导入分享的配置档，请使用 `添加服务器配置`（Add profile）→ `扫描二维码`（Scan QR code）或 `从剪切板导入`（Import from clipboard）。来自剪贴板或二维码的 Throne 链接会直接导入，不会询问。

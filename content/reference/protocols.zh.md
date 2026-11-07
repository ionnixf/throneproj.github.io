+++
title = "协议与导入格式"
description = "配置档类型、Throne 可以导入的链接和文件，以及如何分享配置档。"
weight = 10
toc = true
+++

## 配置档类型 {#protocols}

使用 `程序`（Program）→ `新建配置档`（New profile，快捷键 `Ctrl+N`）创建配置档，在 Android 上使用 `添加服务器配置`（Add profile）→ `手动输入`（Manual settings）。已有配置档的类型无法更改。`直连`（Direct）、`链式代理`（Chain Proxy）、`额外核心`（Extra Core）和各种 `自定义`（Custom）类型仅在高级模式下显示，参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)。

| 类型 | 说明 |
|---|---|
| `自动选择器`（Auto Selector） | 从一个分组中挑选最佳的可用配置档，并自动切换，参见[自动选择器](@/guides/testing.zh.md#auto-selector)。 |
| `Socks` | SOCKS 版本 5 或 4。 |
| `HTTP` | 可选择通过 TLS（HTTPS）传输。 |
| `Shadowsocks` | 支持 2022 系列加密方式，以及 `obfs-local` 和 `v2ray-plugin` 插件。 |
| `VMess` | |
| `Trojan` | |
| `VLESS` | sing-box 实现，支持 REALITY。 |
| `VLESS (Xray)` | Xray 实现：支持 XHTTP、REALITY、VLESS 加密、Finalmask、[XDRIVE](@/advanced/xray.zh.md#xdrive) 传输，以及带 [XDNS](@/advanced/dns_tunnels.zh.md#xdns) 的 mKCP。 |
| `Hysteria` | Hysteria 1 和 2 合为一种类型：通过 `协议版本`（Protocol Version）选择。支持端口跳跃和混淆。当 `上行 (Mbps)`（Up Mbps）或 `下行 (Mbps)`（Down Mbps）留空时，Hysteria 1 使用上行 10 Mbps、下行 50 Mbps，因此请填写你线路的真实速度。 |
| `TUIC` | |
| `Juicity` | |
| `Naive` | 基于 HTTPS 或 QUIC 的 NaïveProxy。Android 上为 `Naïve`。 |
| `TrustTunnel` | |
| `AnyTLS` | |
| `Mieru` | |
| `Snell` | Snell 版本 4 或 6。 |
| `ShadowTLS` | |
| `WireGuard` | 如需使用 AmneziaWG，请勾选 `启用 Amnezia`（Enable Amnezia；Android 上为 `启用 AmneziaWG`（Enable AmneziaWG））。`Amnezia 选项`（Amnezia Options）→ `Generate QUIC Signature...` 会让握手看起来像是到你所选域名的 HTTP/3 连接。可以创建 Cloudflare WARP 账户，参见 [WARP](@/advanced/warp.zh.md#generate)。 |
| `MASQUE` | HTTP/3 或 HTTP/2。可以创建 Cloudflare WARP 身份。 |
| `OpenVPN` | 可导入 `.ovpn` 文件。一次性密码（OTP）仅在桌面版上可用。参见 [OpenVPN](@/advanced/vpn_profiles.zh.md#openvpn)。 |
| `OpenConnect` | 支持 AnyConnect、GlobalProtect、Fortinet、F5、Pulse 和 Juniper（`nc`）服务器。OTP 仅在桌面版上可用。参见 [OpenConnect](@/advanced/vpn_profiles.zh.md#openconnect)。 |
| `Tailscale` | 仅限桌面版。加入你的 Tailscale 网络，并可使用出口节点，参见 [Tailscale](@/advanced/vpn_profiles.zh.md#tailscale)。 |
| `SSH` | |
| `DNSTT / VayDNS` | 连接 dnstt 或 VayDNS 服务器的 DNS 隧道，参见 [DNSTT 和 VayDNS](@/advanced/dns_tunnels.zh.md#dnstt)。 |
| `MasterDNS` | 连接 MasterDnsVPN 或 StormDNS 服务器的 DNS 隧道，参见 [MasterDNS](@/advanced/dns_tunnels.zh.md#masterdns)。 |
| `直连`（Direct） | 不使用代理。设置绑定接口后，流量会通过该网卡发出。 |
| `自定义 (sing-box 出站)`（Custom (sing-box outbound)）、`自定义 (sing-box 配置)`（Custom (sing-box config)） | 你自己的 sing-box JSON：单个出站，或原样运行的完整配置。Android 上为 `自定义配置`（Custom config）。参见[自定义配置](@/advanced/chains.zh.md#custom-config)。 |
| `自定义 (Xray 出站)`（Custom (Xray outbound)）、`自定义 (Xray 配置)`（Custom (Xray config)） | 同上，用于 Xray JSON。 |
| `额外核心`（Extra Core） | 仅限桌面版。运行你选择的外部程序，并连接到它的本地 SOCKS 端口，参见[额外核心](@/advanced/chains.zh.md#extra-core)。 |
| `链式代理`（Chain Proxy） | 让流量依次经过多个配置档。Android 上为 `链式代理`（Proxy chain）。参见[代理链](@/advanced/chains.zh.md#chains)。 |

`AnyTLS`、`Hysteria`、`Juicity`、`MASQUE`、`ShadowTLS`、`TrustTunnel` 和 `TUIC` 始终使用 TLS，因此它们的编辑器中没有关闭 TLS 的开关。

### 由哪个引擎运行配置档 {#engines}

所有类型都在 sing-box 中运行，但 `VLESS (Xray)`、`自定义 (Xray 出站)`（Custom (Xray outbound)）和 `自定义 (Xray 配置)`（Custom (Xray config)）在核心内置的 Xray 引擎中运行（XDNS 也是，因为它是 `VLESS (Xray)` 配置档），`额外核心`（Extra Core）则运行你选择的程序。Throne for Android 使用相同的核心。

`设置`（Settings）→ `基本设置`（Basic Settings）→ `核心`（Core）→ `Xray VLESS 首选项`（Xray VLESS Preference，仅在高级模式下显示）决定导入的 `vless://` 链接成为 `VLESS` 还是 `VLESS (Xray)`。需要仅 Xray 才有的功能（例如 VLESS 加密或 mKCP `type=kcp`）的链接，总是成为 `VLESS (Xray)`。参见 [sing-box 与 Xray](@/advanced/xray.zh.md#vless-preference)。

## 导入格式 {#import-formats}

### 从哪里导入 {#where-to-import}

| 来源 | 桌面版 | Android |
|---|---|---|
| 剪贴板 | `Ctrl+V`，或 `程序` → `添加剪贴板中的配置档`（Add profile from clipboard） | `添加服务器配置` → `从剪切板导入`（Import from clipboard） |
| 文件 | `程序` → `添加文件中的配置档`（Add profile from File(s)，快捷键 `Ctrl+O`），或将文件拖到主窗口上 | `添加服务器配置` → `从文件中导入`（Import from file） |
| 二维码 | `程序` → `扫描二维码`（Scan QR Code，快捷键 `Ctrl+Shift+Q`）可读取屏幕上显示的二维码。二维码图片也可以作为文件导入。 | `添加服务器配置` → `扫描二维码`（Scan QR code）：使用相机，或点按 `从相册选择图片`（Select image） |
| 订阅 | 类型为 `订阅`（Subscription）的分组，参见[订阅与分组](@/guides/subscriptions.zh.md#add-subscription) | 同上，位于 `分组`（Groups）中 |
| 链接 | `throne://` 链接，参见[深度链接](@/advanced/deeplinks.zh.md) | 在其他应用中打开的 `throne://` 链接和协议链接 |

导入的配置档会放入当前分组。如果剪贴板中只有一个 `http://` 或 `https://` URL，Throne 会询问是将它添加为订阅分组、一次性导入到当前分组，还是作为 HTTP 代理使用。

### 支持的格式 {#formats}

两个应用都能在粘贴的文本、文件、二维码和订阅响应中识别以下格式。

| 格式 | 结果 |
|---|---|
| 分享链接，每行一个：`vless://`、`vmess://`、`ss://`、`trojan://`、`socks://`、`socks4://`、`socks4a://`、`socks5://`、`http://`、`https://`、`hysteria://`、`hysteria2://`、`hy2://`、`tuic://`、`juicity://`、`anytls://`、`mieru://`、`mierus://`、`snell://`、`tt://`（TrustTunnel）、`shadowtls://`、`wg://`、`wireguard://`、`awg://`、`ssh://`、`naive+https://`、`naive+quic://` | 每个链接生成一个配置档 |
| Throne 链接：`throne://add/…` 以及较旧的 `json://…` | 一个任意类型的配置档 |
| AmneziaVPN 链接：`vpn://…` | 链接中包含的各个配置 |
| DNS 隧道链接：`dnst://`、`slipnet://`、`masterdns://`、`stormdns://` | 每个链接生成一个 `DNSTT / VayDNS` 或 `MasterDNS` 配置档，参见 [DNS 隧道](@/advanced/dns_tunnels.zh.md#import) |
| sing-box JSON：完整配置，或出站数组 | 每个受支持的出站生成一个配置档，包括 `dnstt` 和 `masterdns` |
| sing-box JSON：单个出站对象 | 一个 `自定义 (sing-box 出站)` 配置档 |
| Xray JSON：配置、出站数组或单个出站 | VLESS 出站生成 `VLESS (Xray)`，其他出站生成 `自定义 (Xray 出站)` |
| Xray JSON：完整配置的数组 | 每个配置生成一个 `自定义 (Xray 配置)` 配置档 |
| SIP008 JSON（Shadowsocks） | `Shadowsocks` 配置档 |
| 含有 `proxies:` 列表的 Clash 或 Mihomo YAML | 为 `socks5`、`http`、`ss`、`vmess`、`vless`、`trojan`、`anytls`、`snell`、`hysteria`、`hysteria2`、`tuic`、`masque` 和 `ssh` 条目生成配置档 |
| WireGuard 或 AmneziaWG 的 `.conf` 文件 | 一个 `WireGuard` 配置档，以 `# Name =` 或 `# remarks:` 注释命名，或者以 `[Peer]` 上方的注释命名 |
| OpenVPN 的 `.ovpn` 文件 | 一个 `OpenVPN` 配置档 |
| AnyConnect XML 配置文件、`openconnect` 命令行，或含有 `protocol=` 行的文件 | `OpenConnect` 配置档 |
| 以上任意格式经 Base64 编码后的内容 | 先解码，再导入 |

`tt://` 包括两种 TrustTunnel 链接：`tt://user:password@server:port?…`，以及 TrustTunnel 应用生成的 `tt://?…` 深度链接（第 1 版和第 2 版）。链接中必须包含服务器地址、服务器名称、用户名和密码，因此只带有订阅地址的链接会被拒绝。如果链接列出了多个服务器地址，Throne 会导入第一个，并对其余地址给出警告。

不是代理服务器的条目会被跳过，例如 sing-box 的 `direct`、`block`、`selector` 和 `urltest` 出站，或 Xray 的 `freedom` 和 `blackhole` 出站。要运行完整的 sing-box 或 Xray 配置，请把它粘贴到 `自定义 (sing-box 配置)`（Custom (sing-box config)）或 `自定义 (Xray 配置)`（Custom (Xray config)）配置档中，参见[完整配置](@/advanced/chains.zh.md#full-configs)。

大于 50 MB 的导入文件（在 Android 上，`.zip` 文件不受此限制）和大于 64 MB 的订阅响应会被拒绝。

### 无法使用的值 {#unusable-values}

以下值不会导致导入失败。Throne 会略去该值，导入配置档的其余部分，并在[变更报告](@/guides/subscriptions.zh.md#change-report)中给出警告：

- `VLESS` 或 `VLESS (Xray)` 配置档无法使用的 `flow`，例如用在不支持 Vision 的传输上的 Vision；
- 无效的 XHTTP 设置（无效的 XHTTP 模式会变为 `auto`）；
- 无效的 REALITY `spx`（spider X）；
- 无效的 Mieru 端口或端口范围。

### Android 上的差异 {#android-import}

- 订阅 URL 也可以是指向设备上某个文件的 `content://` 地址。
- `从文件中导入`（Import from file）也接受 `.zip` 文件，并会导入其中的每个文件，例如多个 WireGuard 或 OpenVPN 配置。
- `clash://install-config?url=…` 链接会打开 Throne，并将该 URL 添加为订阅。
- 在其他应用中点按协议链接（`vless://…`）或 `throne://` 链接时，Throne 会打开，并在导入配置档之前先征求你的确认。
- Android 的分享面板只会为二进制文件（例如备份文件）提供 Throne 选项。聊天应用中的链接请复制后使用 `从剪切板导入`（Import from clipboard）导入，其他文件请用 `从文件中导入` 打开。
- `Tailscale` 和 `额外核心`（Extra Core）配置档无法在 Android 上使用。

## 分享链接 {#share-links}

### 链接类型 {#link-types}

| 链接 | 形式 | 谁能识别 |
|---|---|---|
| 标准链接 | `vless://…`、`ss://…`、`hysteria2://…` 等 | Throne 和大多数其他客户端 |
| Throne 链接 | `throne://add/…` | Throne 桌面版和 Throne for Android |
| 旧版 Throne 链接 | `json://…` | Throne，仅可导入 |

**标准链接**使用该协议通常的链接格式。`自动选择器`（Auto Selector）、`链式代理`（Chain Proxy）、各种 `自定义`（Custom）类型、`直连`（Direct）、`额外核心`（Extra Core）、`MASQUE`、`OpenVPN`、`OpenConnect` 以及使用 XDRIVE 的 `VLESS (Xray)` 没有标准链接，因此 Throne 通常会改为提供 Throne 链接。`Tailscale` 显示的 `ts://` 链接 Throne 无法导入，因此请分享它的 Throne 链接。`DNSTT / VayDNS` 和 `MasterDNS` 配置档会生成 `dnst://` 链接，参见 [DNS 隧道](@/advanced/dns_tunnels.zh.md#import)。

**Throne 链接**以 Base64 编码的 JSON 保存配置档的设置。它适用于所有类型，但只有 Throne 能识别，参见[深度链接](@/advanced/deeplinks.zh.md#add)。`json://` 是它的旧格式，Throne 仍然可以导入。

`链式代理`（Chain Proxy）或 `自动选择器`（Auto Selector）的链接只保存其所用配置档或分组的内部编号，因此只在同一个 Throne 安装中有效。

### 链接中的 ECH 与证书固定 {#ech-links}

Throne 会在标准链接中读写以下 TLS 参数：

| 参数 | 含义 |
|---|---|
| `ech=1` 或 `ech=true` | 开启 ECH。核心会在 DNS 中查找 ECH 配置。 |
| `ech=` 一个域名，或域名加 `+` 再加 DNS 服务器 URL | 开启 ECH，并指定要查询的域名，以及可选的要询问的 DNS 服务器，例如 `cloudflare-ech.com+https://1.1.1.1/dns-query`。它会填入 `ECH Query Name / Resolver`。 |
| `ech=` 其他任意值 | 使用这段 Base64 编码的 ECH 配置开启 ECH。 |
| `pcs=` | 服务器证书的 SHA-256 哈希，十六进制，以逗号分隔。匹配的证书会代替通常的 CA 校验被接受。它会填入 `证书 SHA256`（Certificate SHA256）。 |

对于 `VLESS (Xray)` 配置档，`ech` 的值会原样填入 `ECH Config List`。Throne 生成的链接也会保留旧的 `ech_enabled`、`ech_config` 和 `ech_server_name` 参数。关于 ECH 的作用，请参见 [ECH](@/advanced/presets.zh.md#ech)。

### 在桌面版上分享 {#share-desktop}

右键点击一个配置档，然后打开 `分享`（Share）：

| 操作 | 得到的内容 |
|---|---|
| `二维码和链接`（QR Code and link） | 标准链接及其二维码。勾选 `Deep Link` 可获得 Throne 链接。 |
| `复制选定项的链接`（Copy links of selected，快捷键 `Ctrl+C`） | 选中配置档的标准链接，每行一个。 |
| `复制选定项的链接 (深度链接)`（Copy links of selected (Deep Links)，快捷键 `Ctrl+Alt+C`） | 选中配置档的 Throne 链接。 |
| `导出配置`（Export config，快捷键 `Ctrl+E`） | Throne 为该配置档生成的核心配置，包含一个 `sing-box` 标签页；对于使用 Xray 的配置档，还有一个 `Xray` 标签页。这是供其他客户端使用的配置，而不是分享链接。 |

要复制整个分组的链接，请使用 `分组`（Groups）→ `编辑当前分组`（Edit current Group）→ `复制配置档分享链接`（Copy profile share links）或 `复制配置档分享链接(深度链接)`（Copy profile share links (Deep Links)）。`复制选定项的链接 (深度链接)` 和 `导出配置` 仅在高级模式下显示，参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)。

### 在 Android 上分享 {#share-android}

点按配置档的分享按钮；双列卡片和 `紧凑配置行`（Compact profile rows）则点按 ⋮ → `分享`（Share）：

- `二维码`（QR code）→ `标准`（Standard）或 `Throne 链接`（Throne link）
- `导出到剪切板`（Export to clipboard）→ `标准` 或 `Throne 链接`
- `配置`（Configuration）→ `导出到剪切板` 或 `导出到文件`（Export to file）：Throne 生成的核心配置，而不是分享链接

如果配置档类型没有标准链接，就不会出现 `标准`；代理链只有 `配置`。要复制整个分组的链接，请打开 `分组`（Groups），点按该分组上的 `更多选项`（More options），然后选择 `复制配置分享链接`（Copy profile share links）或 `复制配置分享链接（深度链接）`（Copy profile share links (deep links)）。

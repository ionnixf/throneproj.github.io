+++
title = "DNS 隧道"
description = "在只放行 DNS 的网络中，通过 DNS 查询连接你的服务器：DNSTT、VayDNS、MasterDNS 和 Xray XDNS 配置档。"
weight = 45
toc = true
+++

DNS 隧道把你的流量放在 DNS 查询中传输，因此适用于除 DNS 之外一切都被封锁的网络。它比普通配置档慢得多，首次连接也需要一段时间。只有在其他配置档都无法连接时才使用它。

## DNS 隧道的工作方式 {#how-it-works}

Throne 并不直接连接隧道服务器。它把隧道域名（例如 `t.example.com`）下的名称的 DNS 查询发送给解析器：你所在网络的 DNS 服务器，或 `8.8.8.8` 这样的公共服务器。该域名被委派给隧道服务器，因此解析器会把查询转交给它，服务器在 DNS 应答中回复。

所以 DNS 隧道配置档没有服务器地址和端口。它需要隧道域名、服务器的密钥，以及能转交查询的[解析器](#resolvers)。速度主要取决于解析器：许多解析器会限制查询的大小或数量，或者丢弃不常见的查询。

| 种类 | `类型`（Type） | 服务器 |
| --- | --- | --- |
| [DNSTT 和 VayDNS](#dnstt) | `DNSTT / VayDNS` | `dnstt-server` 或 `vaydns-server` |
| [MasterDNS](#masterdns) | `MasterDNS` | MasterDnsVPN 或 StormDNS |
| [XDNS](#xdns) | 使用 `kcp` 网络和 `xdns` 掩码的 `VLESS (Xray)` | 带 mKCP 和 `xdns` 掩码的 Xray |

通过 `程序`（Program）→ `新建配置档`（New profile）（`Ctrl+N`）创建配置档，或[导入](#import)链接或配置。关于 Throne for Android，参见 [Android](#android)。

## DNSTT 和 VayDNS {#dnstt}

`模式`（Mode）用于选择协议：`DNSTT`（默认）对应 `dnstt-server` 或 `vaydns-server -dnstt-compat`，`VayDNS` 对应 `vaydns-server`，`VayDNS (兼容 dnstt)`（VayDNS (dnstt-compatible)）对应与 `DNSTT` 相同的服务器，并提供 `最大 QNAME 长度`（Max QNAME Length）。

| 字段 | 作用 |
| --- | --- |
| `域名`（Domain） | 委派给服务器的隧道域名，例如 `t.example.com`。 |
| `公钥`（Public Key） | 服务器的公钥：64 个十六进制数字。 |
| `记录类型`（Record Type）、`客户端 ID 长度`（Client ID Size） | 仅 `VayDNS`。必须与服务器一致（ID 长度对应 `-clientid-size`）。 |
| `最大 QNAME 长度` | 两种 VayDNS 模式。默认 101（`VayDNS`）或 253（兼容 dnstt），最多 253。名称越长，携带的数据越多，但有些解析器会丢弃过长的名称。 |
| `每次查询载荷`（Payload per Query） | 每次查询携带的隧道数据。至少需要 25 字节：请使用更短的域名或更大的 `最大 QNAME 长度`。 |

在 `高级`（Advanced）组中（仅在高级模式下显示，参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)），请让 `空闲超时`（Idle Timeout）与服务器保持一致；`保活间隔`（Keepalive Interval）必须短于它。

`后端`（Backend）→ `类型`（Type）说明服务器上隧道后面运行的是什么：

- `无 (仅用于链式代理)`（None (chain only)）（默认）：服务器把隧道转交给另一台代理服务器。请创建一个[代理链](@/advanced/chains.zh.md#create-chain)，DNS 隧道配置档在上，那台服务器的配置档在下。
- `SOCKS5` 或 `Shadowsocks`：填写其登录凭据。勾选 `基于 TCP 的 UDP`（UDP over TCP）以传输 UDP。
- `SSH`：与 `SSH` 配置档一样，填写 `用户`（User）以及 `密码`（Password）或 `私钥`（Private Key）。

## MasterDNS {#masterdns}

`MasterDNS` 配置档可配合 MasterDnsVPN 和 StormDNS 服务器使用，只传输 TCP。以下字段必须与服务器的配置一致：

| 字段 | 服务器设置 | 填写内容 |
| --- | --- | --- |
| `域名`（Domains） | `DOMAIN` | 每行一个，都指向同一台服务器。 |
| `模式`（Mode） | `PROTOCOL_TYPE` | `SOCKS5`（默认）：由服务器替你连接网站。`TCP / 自定义链`（TCP / custom chain）：服务器把每个连接发往它自己的转发目标，因此请把该目标的配置档链在此配置档之下。 |
| `加密`（Encryption） | `DATA_ENCRYPTION_METHOD` | `none`（0）、`xor`（1，默认）、`chacha20`（2）、`aes-128-gcm`（3）、`aes-192-gcm`（4）或 `aes-256-gcm`（5）。 |
| `密钥`（Key） | `ENCRYPTION_KEY_FILE` | 该文件中的文本。使用 `none` 时不需要。 |

{% alert_warning() %}
MasterDNS 只加密查询。服务器的应答从不加密，也不做认证；`xor` 只是混淆，`chacha20` 不带认证。请只让加密流量通过该隧道，例如 HTTPS 网站，或者在 MasterDNS 配置档之下链接一个加密代理。
{% end %}

在 `数据传输`（Transfer）组中，`重复发送`（Duplication）（默认 3，最多 10）会把每个数据包经由这么多对解析器与域名发送。服务器可能会拒绝压缩。`Base64 编码的应答`（Base64-encoded replies）适用于会损坏二进制 TXT 记录的解析器，但携带的数据少四分之一。

会话开始前，Throne 会测试每一对域名与解析器，以确定一次查询和一次应答能携带多少数据。低于 `MTU` 组中 `最小上传`（Min Upload）或 `最小下载`（Min Download）（38 和 200 字节）的解析器不会被使用。如果没有解析器通过，Throne 每 5 秒重复一次测试。

## XDNS {#xdns}

XDNS 就是在最终掩码（Finalmask）中带有 `xdns` 掩码的 Xray mKCP 传输：Xray 把 mKCP 数据包作为 DNS 查询经由解析器发送。服务器必须运行 Xray，并配置 mKCP 入站和针对同一域名的 `xdns` 掩码。

最简单的方式是导入：带有 `type=kcp` 且在 `fm` 中带有掩码的 `vless://` 链接、服务器提供的 Xray JSON，或 Throne 链接。无论[为 VLESS 链接选择核心](@/advanced/xray.zh.md#vless-preference)如何设置，带 `type=kcp` 的 `vless://` 链接总是会成为 `VLESS (Xray)` 配置档。

手动设置：

1. 打开 `程序`（Program）→ `新建配置档`（New profile），将 `类型`（Type）设为 `VLESS (Xray)`，然后填写服务器的 VLESS 设置。
2. 将 `传输(网络)`（Network）设为 `kcp`。
3. 点击 `最终掩码`（Finalmask），在 `udp` 列表中添加一个 `xdns` 掩码，填入隧道域名，并可选填解析器：

   ```json
   {
     "udp": [
       {
         "type": "xdns",
         "settings": {
           "domains": [{ "names": ["t.example.com"] }],
           "resolvers": [{ "addrs": ["udp://8.8.8.8:53"] }]
         }
       }
     ]
   }
   ```

4. 可选：在 `解析器`（Resolvers）一行中填写解析器。它们会替换掩码中的解析器。
5. 点击 `确定`（OK）。

`xdns` 掩码必须是 `udp` 中的最后一个掩码，并且至少需要一个域名。如果掩码和 `解析器` 一行中都没有解析器，Xray 无法连接。掩码中的 DNS-over-HTTPS 解析器（`https://…`）需要带 `serverName` 的 `tlsConfig`，例如 `"tlsConfig": {"serverName": "dns.google"}`。

mKCP 字段（例如 `MTU` 和 `TTI`）留空即使用 Xray 的默认值。当 `多路复用`（Multiplex）为 `保持默认`（Keep Default）时，Throne 会为 XDNS 配置档开启 mux；没有 mux 时，每个连接都会单独打开一个连向所有解析器的 mKCP 会话。

## 解析器 {#resolvers}

每个 DNS 隧道配置档都有一个解析器文本框（DNSTT 和 MasterDNS 是 `解析器`（Resolvers）组，XDNS 是 `解析器` 一行），每行一个解析器。以 `#` 开头的行是注释。`IP 列表`（IP list）会在手动输入的各行之后加入 IP 扫描器中某个列表的地址，例如[解析器扫描](#find-resolvers)的结果。

| 行 | DNSTT / VayDNS | MasterDNS | XDNS |
| --- | --- | --- | --- |
| `system`：系统的 DNS 服务器 | 是 | 是 | 否 |
| IP 地址，可带端口：`8.8.8.8`、`1.1.1.1:53`、`[2001:db8::1]:53` | 是 | 是 | 是 |
| 地址范围，例如 `192.0.2.0/24` | 最多 256 个地址（`/24` 或更长） | 最多 65536 个地址（`/16` 或更长） | 只能来自 IP 列表，最多 256 个地址 |
| `udp://` 或 `tcp://` 加地址 | 是 | 否 | 是 |
| `tls://`（DNS over TLS） | 是 | 否 | 否 |
| `https://`（DNS over HTTPS），可带路径 | 是 | 否 | 是 |

默认端口为 53；DNSTT 的 `tls://` 使用 853，`https://` 使用 443。DNSTT 最多使用 4096 个解析器，XDNS 最多 256 个。没有任何解析器时，DNSTT 和 MasterDNS 使用 `system`，XDNS 保留 `xdns` 掩码中的解析器。

`策略`（Strategy）决定查询发往哪里：

- DNSTT 和 VayDNS：`round_robin`（默认）依次使用每个解析器，`failover` 使用第一个可用的解析器，`fanout` 同时使用每个可用的解析器。
- MasterDNS：`least_loss`（默认）使用丢包最少的解析器。其他策略依次、随机、按延迟或按丢包与延迟的综合情况选择。
- XDNS：Xray 依次使用所有解析器。

## 查找可用的解析器 {#find-resolvers}

许多解析器不转交隧道查询。解析器扫描会把各个地址当作你的配置档的解析器逐一尝试，并保留可用的地址：

1. 保存配置档并再次打开。在保存之前，`查找解析器…`（Find resolvers…）处于禁用状态。
2. 点击 `IP 列表`（IP list）旁的 `查找解析器…`。Throne 会在 IP 扫描器中打开此配置档的 DNS 解析器扫描。
3. 在 `Targets` 标签页的 `Base IP list` 中选择要尝试的地址，例如 `Iran DNS resolvers` 或 `Russia DNS resolvers`，然后开始扫描。
4. 在 `Results` 标签页中点击 `用作解析器`（Use as resolvers）。结果会成为该配置档的 `IP 列表`。你输入的各行会保留，并优先使用。

参见 [DNS 解析器扫描](@/guides/testing.zh.md#dns-scan)。

## 导入与分享 {#import}

Throne 可以从剪贴板、文件、二维码和订阅中导入以下格式：

| 格式 | 结果 |
| --- | --- |
| `dnst://` 链接 | `DNSTT / VayDNS` 配置档；链接中的隧道类型为 `masterdns` 时为 `MasterDNS` 配置档。 |
| SlipNet 16 到 28 版的 `slipnet://` 链接 | `DNSTT / VayDNS` 配置档。NoizDNS 配置档会变成普通的 DNSTT，没有 NoizDNS 的 DPI 规避功能。无法读取加密的 SlipNet 链接。 |
| `masterdns://` 和 `stormdns://` 链接 | `MasterDNS` 配置档。 |
| 带 `dnstt` 或 `masterdns` 出站的 sing-box JSON | `DNSTT / VayDNS` 和 `MasterDNS` 配置档。 |
| 带 `type=kcp` 的 `vless://` 链接、使用 `kcp` 网络的 Xray JSON | `VLESS (Xray)` 配置档；带 `xdns` 掩码时为 XDNS 配置档。 |
| `throne://add/` 链接 | 以上任意一种，包含全部设置。 |

- `dnst://` 链接会注明隧道后面的服务：`socks`、`ssh`、`shadowsocks`，或表示 `无 (仅用于链式代理)` 的 `custom`。MasterDNS 只接受 `socks` 和 `custom`。
- 如果链接还带有一个要在隧道上运行的代理，Throne 会给出提示。请单独导入该代理，并把它链在 DNS 隧道配置档之下。

要分享配置档，请右键点击它 → `分享`（Share）。DNSTT 和 MasterDNS 配置档会生成 `dnst://` 链接，XDNS 配置档会生成 `vless://` 链接。这些链接不包含你的解析器和 IP 列表；对于 MasterDNS，只包含第一个域名。Throne 链接保留除 IP 列表之外的所有设置。参见[分享链接](@/reference/protocols.zh.md#share-links)。

## 测试 {#testing}

URL 测试和 IP 测试都适用于 DNS 隧道配置档。第一个请求会建立隧道，这需要许多缓慢的查询，因此 Throne 为它额外留出时间：DNSTT 和 XDNS 多 30 秒，MasterDNS 多 90 秒；代理链得到它们的总和。显示的延迟来自第二个请求。参见 [URL 测试](@/guides/testing.zh.md#url-test)。

如果测试失败，请在 `日志`（Logs）标签页中查看原因，对照服务器检查 `域名` 和 `公钥`（DNSTT）或 `域名`、`加密` 和 `密钥`（MasterDNS），并尝试其他解析器或[解析器扫描](#find-resolvers)。

## Android {#android}

Throne for Android 有同样的 DNS 隧道，也能导入同样的链接，包括在其他应用中点按的链接。区别：

- 通过 `添加服务器配置`（Add profile）→ `手动输入`（Manual settings）→ `DNSTT / VayDNS` 或 `MasterDNS` 创建配置档。对于 XDNS，选择 `VLESS (Xray)`，将 `传输协议`（Network）设为 `mKCP`，并在 `Finalmask` 中添加 `xdns` 掩码。
- 没有 IP 列表，也没有解析器扫描：只使用配置档中手动输入的解析器。
- URL 测试不会为第一个请求额外留出时间。

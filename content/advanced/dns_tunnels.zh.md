+++
title = "DNS 隧道"
description = "在只放行 DNS 的网络中，通过 DNS 查询连接你的服务器：DNSTT、VayDNS、MasterDNS 和 Xray XDNS 配置档、它们的解析器，以及如何找到可用的解析器。"
weight = 45
toc = true
+++

自 1.4.0 起，Throne 可以把你的流量放在 DNS 查询中传输。DNS 隧道适用于除 DNS 之外一切都被封锁的网络。它比普通配置档慢得多，首次连接也需要一段时间。只有在其他配置档都无法连接时才使用它。

## DNS 隧道的工作方式 {#how-it-works}

Throne 并不直接连接隧道服务器。它把隧道域名（例如 `t.example.com`）下的名称的 DNS 查询发送给解析器，也就是 DNS 服务器，例如你所在网络的 DNS 服务器，或 `8.8.8.8` 这样的公共服务器。该域名被委派给隧道服务器，因此解析器会把查询转交给它。服务器在 DNS 应答中发回数据。

这意味着：

- DNS 隧道配置档没有服务器地址和端口。它需要隧道域名、服务器的密钥，以及能转交查询的[解析器](#resolvers)。
- 速度主要取决于解析器。许多解析器会限制查询的大小或数量，或者丢弃不常见的查询。[查找可用的解析器](#find-resolvers)可以帮你挑出好用的解析器。
- 服务器需要一个其子域名委派给它的域名，以及与配置档类型相匹配的服务端程序。

Throne 支持三种 DNS 隧道：

| 种类 | `新建配置档`（New profile）中的 `类型`（Type） | 服务器 | 核心 |
| --- | --- | --- | --- |
| [DNSTT 和 VayDNS](#dnstt) | `DNSTT / VayDNS` | `dnstt-server` 或 `vaydns-server` | sing-box |
| [MasterDNS](#masterdns) | `MasterDNS` | MasterDnsVPN 或 StormDNS | sing-box |
| [XDNS](#xdns) | 使用 `kcp` 网络和 `xdns` 掩码的 `VLESS (Xray)` | 带 mKCP 和 xdns 的 Xray | Xray |

要手动创建配置档，请打开 `程序`（Program）→ `新建配置档`（New profile）（`Ctrl+N`）并选择类型。你也可以导入链接和配置，参见[导入与分享](#import)。DNS 隧道仅在桌面版中可用，参见 [Android](#android)。

## DNSTT 和 VayDNS {#dnstt}

`DNSTT / VayDNS` 配置档可配合 dnstt 和 VayDNS 服务器使用。`模式`（Mode）用于选择协议：

| `模式` | 服务器 |
| --- | --- |
| `DNSTT`（默认） | `dnstt-server`，或 `vaydns-server -dnstt-compat` |
| `VayDNS` | `vaydns-server` |
| `VayDNS (兼容 dnstt)`（VayDNS (dnstt-compatible)） | 与 `DNSTT` 相同的服务器。客户端的行为与 `vaydns-client -dnstt-compat` 相同，并提供 `最大 QNAME 长度`（Max QNAME Length）。 |

隧道设置：

| 字段 | 默认值 | 作用 |
| --- | --- | --- |
| `域名`（Domain） | 必填 | 委派给服务器的隧道域名，例如 `t.example.com`。 |
| `公钥`（Public Key） | 必填 | 服务器的公钥：64 个十六进制数字，与服务器输出的一致。 |
| `记录类型`（Record Type） | `默认`（Default）（TXT） | 仅 `VayDNS`。服务器用来携带数据的 DNS 记录类型：TXT、NULL、CNAME、A、AAAA、MX、NS、SRV 或 CAA。请使用服务器所用的类型。其他模式始终使用 TXT。 |
| `客户端 ID 长度`（Client ID Size） | 2 | 仅 `VayDNS`。客户端 ID 的字节数，必须与服务器的 `-clientid-size` 一致。 |
| `最大 QNAME 长度`（Max QNAME Length） | 101（`VayDNS`），253（`VayDNS (兼容 dnstt)`） | 两种 VayDNS 模式。查询名称的最大长度，最多 253。名称越长，携带的数据越多，但有些解析器会丢弃过长的名称。 |
| `每次查询载荷`（Payload per Query） | | 显示每次查询能携带多少字节的隧道数据。域名越长，剩余空间越少。隧道至少需要 25 字节；低于此值时编辑器会显示错误。请使用更短的域名或更大的 `最大 QNAME 长度`。 |

`解析器`（Resolvers）组包含解析器（参见[解析器](#resolvers)）和 `策略`（Strategy）（参见[解析器的选择方式](#strategy)）。`通过 TCP 重试被截断的应答`（Retry truncated replies over TCP）会在应答被截断时，通过 TCP 再次向该 UDP 解析器查询。

`高级`（Advanced）组（仅在高级模式下显示，参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）：

| 字段 | 默认值 | 作用 |
| --- | --- | --- |
| `空闲超时`（Idle Timeout） | `2m`，`VayDNS` 下为 `10s` | 在这段时间内没有收到任何数据时，会话将关闭。请与服务器保持一致。 |
| `保活间隔`（Keepalive Interval） | `10s`，`VayDNS` 下为 `2s` | 客户端发送保活的频率。必须短于 `空闲超时`。 |
| `每秒查询数`（Queries per Second） | 留空，不限制 | 隧道每秒最多发送的查询数。 |

### 后端 {#dnstt-backend}

`后端`（Backend）组说明服务器上隧道后面运行的是什么。请选择服务器所用的 `类型`（Type）：

| `类型` | 作用 |
| --- | --- |
| `无 (仅用于链式代理)`（None (chain only)）（默认） | 服务器把隧道转交给另一台代理服务器。编辑器会提示：只有链在此配置档之上的配置档才能使用该隧道。请创建一个[代理链](@/advanced/chains.zh.md#create-chain)，DNS 隧道配置档在上，隧道后面那台代理服务器的配置档在下。 |
| `SOCKS5` | 隧道后面的 SOCKS5 代理。如有需要，请填写 `用户名`（Username）和 `密码`（Password）。勾选 `基于 TCP 的 UDP`（UDP over TCP）以传输 UDP。 |
| `Shadowsocks` | 隧道后面的 Shadowsocks 服务器：`加密`（Encryption）和 `密码`（Password）。勾选 `基于 TCP 的 UDP`（UDP over TCP）以传输 UDP。 |
| `SSH` | 隧道后面的 SSH 服务器：`用户`（User），以及 `密码`（Password）或 `私钥`（Private Key）（或通过 `选择文件`（Choose File）填写 `私钥路径`（Private Key Path））、`私钥密码`（Private Key Password）。`主机钥匙`（Host Keys）、`主机钥匙算法`（Host Key Algorithms）和 `客户端版本`（Client Version）与 `SSH` 配置档中的相同。 |

## MasterDNS {#masterdns}

`MasterDNS` 配置档可配合 MasterDnsVPN 和 StormDNS 服务器使用，只传输 TCP。设置必须与服务器的配置一致：

| 字段 | 默认值 | 作用 |
| --- | --- | --- |
| `域名`（Domains） | 必填 | 服务器的隧道域名（其 `DOMAIN` 设置），每行一个。它们必须都指向同一台服务器。Throne 会把查询分散到每一对域名与解析器上。 |
| `模式`（Mode） | `SOCKS5` | 必须与服务器的 `PROTOCOL_TYPE` 一致。`SOCKS5`：由服务器替你连接网站。`TCP / 自定义链`（TCP / custom chain）：服务器把每个连接发往它自己的转发目标，因此请把该目标的配置档链在此配置档之下，与上文的 `无 (仅用于链式代理)` 相同。 |
| `加密`（Encryption） | `xor` | 必须与服务器的 `DATA_ENCRYPTION_METHOD` 一致：`none`（0）、`xor`（1）、`chacha20`（2）、`aes-128-gcm`（3）、`aes-192-gcm`（4）或 `aes-256-gcm`（5）。 |
| `密钥`（Key） | 必填（`none` 除外） | 服务器的密钥，即其 `ENCRYPTION_KEY_FILE` 中的文本。 |

{% alert_warning() %}
MasterDNS 只加密查询。服务器的应答从不加密，也不做认证；`xor` 只是混淆，`chacha20` 不带认证。请只让加密流量通过该隧道，例如 HTTPS 网站，或者在 MasterDNS 配置档之下链接一个加密代理。开启 `设置`（Settings）→ `基本设置`（Basic Settings）→ `样式`（Style）→ `显示配置安全信息`（Show Config Security；仅在高级模式下显示）后，这些配置档的 `类型` 列会显示 `弱密码算法`（Weak Cipher）、`明文应答`（Plaintext Replies）（AES-GCM）或 `原始`（Raw）（`none`）。
{% end %}

`数据传输`（Transfer）组：

| 字段 | 默认值 | 作用 |
| --- | --- | --- |
| `重复发送`（Duplication） | 3 | Throne 为每个数据包发送多少份副本，每份经由不同的解析器与域名组合，最多 10。服务器可能会调低此值。 |
| `建连重复发送`（Setup Duplication） | 4 | 建立连接的数据包的副本数，从 `重复发送` 的值到 12。 |
| `上传压缩`（Upload Compression）、`下载压缩`（Download Compression） | `默认`（关闭） | `关闭`（Off）、`zstd`、`lz4` 或 `zlib`。服务器可能会拒绝。 |
| `Base64 编码的应答`（Base64-encoded replies） | 关闭 | 要求服务器用 Base64 应答，适用于会损坏二进制 TXT 记录的解析器。Base64 应答携带的数据少四分之一。 |

会话开始前，Throne 会测试每一对域名与解析器，以确定一次查询（上传）和一次应答（下载）能携带多少数据。会话使用通过测试的解析器中的最小值。`MTU` 组设置该测试的范围：

| 字段 | 默认值 | 作用 |
| --- | --- | --- |
| `最小上传`（Min Upload）、`最小下载`（Min Download） | 38、200 | 低于这些字节数的解析器不会被使用。 |
| `最大上传`（Max Upload）、`最大下载`（Max Download） | 150、4000 | Throne 尝试的最大字节数。服务器也可以限制它们。 |

`高级`（Advanced）组（仅在高级模式下显示）包含 `MTU 测试重试次数`（MTU Test Retries）（默认每个大小尝试 2 次）、`MTU 测试超时`（MTU Test Timeout）（默认每次尝试 `2s`）、`MTU 测试并发数`（MTU Test Parallelism）（默认同时测试 32 个解析器）和 `丢弃低于最小 MTU 的解析器`（Drop resolvers below the minimum MTU）（默认开启）。开启该选项且至少有 5 个解析器通过时，Throne 会舍弃少数大小偏低、会拖慢整个会话的解析器。

会话在第一个连接时、上述测试之后开始。如果没有解析器通过，Throne 每 5 秒重复一次测试。未通过的解析器会在后台再次测试。

## XDNS {#xdns}

自 1.4.0 起，`VLESS (Xray)` 配置档可以使用 mKCP 传输，它通过 UDP 承载连接。XDNS 就是在最终掩码（Finalmask）中带有 `xdns` 掩码的 mKCP：Xray 把 mKCP 数据包作为 DNS 查询经由解析器发送。服务器必须运行 Xray，并配置 mKCP 入站和针对同一域名的 `xdns` 掩码。

获取 XDNS 配置档最简单的方式是导入：带有 `type=kcp` 且在 `fm` 中带有最终掩码的 `vless://` 链接、服务器提供的 Xray JSON 配置，或 Throne 链接。无论[为 VLESS 链接选择核心](@/advanced/xray.zh.md#vless-preference)如何设置，带 `type=kcp` 的 `vless://` 链接总是会成为 `VLESS (Xray)` 配置档。

手动设置 XDNS：

1. 打开 `程序`（Program）→ `新建配置档`（New profile），将 `类型`（Type）设为 `VLESS (Xray)`，然后填写服务器提供的 VLESS 设置。
2. 将 `传输(网络)`（Network）设为 `kcp`。mKCP 字段随即出现。
3. 点击 `最终掩码`（Finalmask）按钮，在 `udp` 列表中添加一个 `xdns` 掩码，填入隧道域名，并可选填解析器：

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

4. `MTU` 和 `TTI` 下方会出现 `解析器`（Resolvers）一行。在这里填写的解析器会替换掩码中的解析器，参见[解析器](#resolvers)。
5. 点击 `确定`（OK）。

编辑器会检查掩码。`xdns` 掩码必须是 `udp` 中的最后一个掩码，并且至少需要一个域名。如果掩码和 `解析器` 一行中都没有解析器，编辑器会提示 Xray 无法连接。掩码中的 DNS-over-HTTPS 解析器（`https://…`）需要带 `serverName` 的 `tlsConfig`，例如 `"tlsConfig": {"serverName": "dns.google"}`。

mKCP 字段。留空则使用 Xray 的默认值：

| 字段 | 默认值 | 作用 |
| --- | --- | --- |
| `MTU` | 1350 | mKCP 发送的最大数据包，单位为字节，范围 21 到 8192。 |
| `TTI` | 50 | mKCP 的发送间隔，单位为毫秒，范围 10 到 1000。 |
| `上行容量`（Uplink Capacity） | 5 | 每秒最多发送的数据量，单位为 MB/s。 |
| `下行容量`（Downlink Capacity） | 20 | 每秒最多接收的数据量，单位为 MB/s。 |
| `CWND 倍数`（CWND Multiplier） | 1 | 放大同时在途的数据包数量。 |
| `最大发送窗口`（Max Sending Window） | 2097152 | 等待发送或确认的最大数据量，单位为字节。不小于 `MTU`。 |

后四个字段位于 `调优`（Tuning）组中（仅在高级模式下显示）。在简洁模式下，只有 `kcp` 会显示 `最终掩码` 按钮。

其他须知：

- 当 `多路复用`（Multiplex）为 `保持默认`（Keep Default）时，即使 `默认复用开启`（Default Mux On）处于关闭状态，Throne 也会为 XDNS 配置档开启 mux。没有 mux 时，每个连接都会成为一个单独的 mKCP 会话，并各自与所有解析器建立套接字。
- 旧的 mKCP 链接和配置会设置头部类型（`headerType`）或 `seed`。较新的 Xray 版本已移除这些设置，因此 Throne 在导入时会把它们转换为最终掩码中的 `mkcp-legacy` 掩码，也会更新旧格式的 `xdns` 掩码。
- 开启 `显示配置安全信息`（Show Config Security）后，这些配置档的 `类型` 列会显示 `XDNS`。

不带 `xdns` 掩码时，mKCP 也可以作为普通的 UDP 传输连接 Xray 服务器。

## 解析器 {#resolvers}

每个 DNS 隧道配置档都有解析器设置：DNSTT 和 MasterDNS 是 `解析器`（Resolvers）组，XDNS 是 `解析器` 一行。它们由三部分组成：

- 一个文本框，每行一个解析器。以 `#` 开头的行是注释。
- `IP 列表`（IP list）：IP 扫描器中的一个 IP 列表，例如解析器扫描的结果。它的地址排在文本框中的各行之后使用。没有端口的条目使用端口 53。
- `查找解析器…`（Find resolvers…），参见[查找可用的解析器](#find-resolvers)。

每一行可以填写的内容：

| 行 | DNSTT / VayDNS | MasterDNS | XDNS |
| --- | --- | --- | --- |
| `system`：系统的 DNS 服务器 | 是 | 是 | 否 |
| IP 地址，可带端口：`8.8.8.8`、`1.1.1.1:53`、`[2001:db8::1]:53` | 是 | 是 | 是 |
| 地址范围：`192.0.2.0/24` | 最多 256 个地址（`/24` 或更长，IPv6 为 `/120`） | 最多 65536 个地址（`/16` 或更长，IPv6 为 `/112`） | 只能来自 IP 列表，最多 256 个地址 |
| `udp://` 或 `tcp://` 加地址 | 是 | 否 | 是 |
| `tls://`（DNS over TLS） | 是 | 否 | 否 |
| `https://`（DNS over HTTPS），可带路径 | 是 | 否 | 是 |

默认端口为 53；对于 DNSTT，`tls://` 为 853，`https://` 为 443。DNSTT 的 `tls://` 和 `https://` 解析器使用 uTLS 的 `Default Fingerprint`，参见 [uTLS 指纹](@/advanced/presets.zh.md#utls)。Throne 对每个解析器只使用一次，DNSTT 最多使用 4096 个解析器，XDNS 最多 256 个。

当文本框和 IP 列表都没有提供解析器时：

- DNSTT 和 MasterDNS 使用 `system`。网络变化时，Throne 会重新读取系统的 DNS 服务器，并跳过 TUN 网卡地址范围内的服务器。
- XDNS 保留 `xdns` 掩码中的解析器。一旦你填写了解析器，它们就会替换掩码中的解析器。

Throne 在每次启动配置档时读取 IP 列表，因此下次启动会使用最新的条目。如果 IP 列表被删除，配置档不会从中获得任何解析器：编辑器会显示“这个 IP 列表已不存在，因此不会添加任何解析器。”（This IP list no longer exists, so it adds no resolvers.），配置档启动时日志中也会记录这一点。

### 解析器的选择方式 {#strategy}

DNSTT 和 VayDNS：`解析器` 组中的 `策略`（Strategy）。

| `策略` | 查询发往 |
| --- | --- |
| `默认`（Default）、`round_robin` | 依次发往每个解析器。 |
| `failover` | 第一个可用的解析器。 |
| `fanout` | 同时发往每个可用的解析器。 |

如果某个解析器在 2 秒内有 8 个查询未得到应答，它会被跳过 1 秒。每次再发生这种情况，暂停时间加倍，最长 30 秒。失败后会话会重新开始，每次尝试之间暂停 1 到 30 秒。

MasterDNS：`解析器` 组中的 `策略`（Strategy）。

| `策略` | 查询发往 |
| --- | --- |
| `默认`（Default）、`least_loss` | 丢包最少的解析器。 |
| `round_robin` | 依次发往每个解析器。 |
| `random` | 随机一个解析器。 |
| `lowest_latency` | 延迟最低的解析器。 |
| `hybrid` | 丢包与延迟综合得分最好的解析器。 |
| `loss_then_latency` | 丢包最少的解析器中延迟最低的那个。 |
| `least_loss_top_random` | 丢包最少的前 10% 解析器中随机一个。 |
| `least_loss_top_round_robin` | 依次发往丢包最少的前 10% 解析器。 |

数据包不断被重发的连接会改用另一个解析器，最多每 2.5 秒换一次。持续超时的解析器会被暂停使用，直到再次通过测试。

XDNS：Xray 把查询依次分散到所有解析器上。

## 查找可用的解析器 {#find-resolvers}

许多解析器不转交隧道查询，或者转交得很慢。解析器扫描会把各个地址当作你的配置档的解析器逐一尝试，并保留可用的地址：

1. 保存配置档。在保存之前，`查找解析器…`（Find resolvers…）处于禁用状态，因为扫描测试的是已保存的配置档。
2. 再次打开该配置档，点击 `IP 列表`（IP list）旁的 `查找解析器…`。Throne 会在 IP 扫描器中打开此配置档的 DNS 解析器扫描，或新建一个名为“…的解析器”（Resolvers for …）的扫描，其中 … 为配置档名称。
3. 在 `Targets` 标签页的 `Base IP list` 中选择要尝试的地址，例如默认列表 `Iran DNS resolvers` 或 `Russia DNS resolvers`，然后开始扫描。扫描会把每个地址当作该配置档唯一的解析器来测试。
4. 在 `Results` 标签页中点击 `用作解析器`（Use as resolvers）。扫描的结果列表会成为该配置档的 `IP 列表`。你输入的各行会保留，并优先使用。

如果该配置档的编辑器正处于打开状态，列表会填入编辑器。请在其中点击 `确定`（OK）保存。

关于测试和结果列，参见 [DNS 解析器扫描](@/guides/testing.zh.md#dns-scan)。

## 导入与分享 {#import}

Throne 可以从剪贴板、文件、二维码和订阅中导入以下格式：

| 格式 | 结果 |
| --- | --- |
| `dnst://` 链接 | `DNSTT / VayDNS` 配置档；链接中的隧道类型为 `masterdns` 时为 `MasterDNS` 配置档。 |
| SlipNet 16 到 28 版的 `slipnet://` 链接 | `DNSTT / VayDNS` 配置档。NoizDNS 配置档会变成普通的 DNSTT，其服务器可以接受，但没有 NoizDNS 的 DPI 规避功能。 |
| `masterdns://` 和 `stormdns://` 链接 | `MasterDNS` 配置档。Throne 可以读取带客户端配置的 `v1/…` 形式、`v1?domains=…` 形式，以及 WhiteDNS 和 Hidden-Node 的配置档格式。 |
| 带 `dnstt` 或 `masterdns` 出站的 sing-box JSON | `DNSTT / VayDNS` 和 `MasterDNS` 配置档。 |
| 带 `type=kcp` 的 `vless://` 链接，以及使用 `kcp` 网络的 Xray JSON | `VLESS (Xray)` 配置档。如果最终掩码中带有 `xdns` 掩码，就是 XDNS 配置档。 |
| Throne 链接（`throne://add/…`） | 以上任意一种，包含全部设置。 |

导入须知：

- `dnst://` 链接会注明隧道后面的服务：`socks`、`ssh`、`shadowsocks`，或表示 `无 (仅用于链式代理)` 的 `custom`。MasterDNS 只接受 `socks`（`SOCKS5`）和 `custom`（`TCP / 自定义链`）。
- 无法读取加密的 SlipNet 链接（`slipnet-enc://`、`slipnet-bundle-enc://`）。请在 SlipNet 中不加密地导出配置档。
- 如果链接还带有一个要在隧道上运行的代理，Throne 会给出提示。请单独导入该代理，并把它链在 DNS 隧道配置档之下。
- Throne 链接中指定的 IP 列表属于另一个安装，因此 Throne 不会沿用它。

要分享配置档，请右键点击它 → `分享`（Share）。DNSTT 和 MasterDNS 配置档会生成 `dnst://` 链接，XDNS 配置档会生成 `vless://` 链接。这些链接不包含你的解析器和 IP 列表；对于 MasterDNS，只包含第一个域名。Throne 链接保留除 IP 列表之外的所有设置。参见[分享链接](@/reference/protocols.zh.md#share-links)。

## 测试 {#testing}

URL 测试和 IP 测试都适用于 DNS 隧道配置档。隧道会在第一个请求时建立会话，这需要许多缓慢的查询。因此 Throne 为第一个请求额外留出时间：DNSTT 和 XDNS 配置档多 30 秒，MasterDNS 配置档多 90 秒。包含多个隧道的代理链会得到它们的总和。显示的延迟来自第二个请求，即通过已建立的会话发出的请求。参见 [URL 测试](@/guides/testing.zh.md#url-test)。

如果测试失败：

- 在 `日志`（Logs）标签页中查看原因。
- 对照服务器检查 `域名` 和 `公钥`（DNSTT），或 `域名`、`加密` 和 `密钥`（MasterDNS）。
- 尝试其他解析器，或运行[解析器扫描](#find-resolvers)。

## Android {#android}

Throne for Android 没有 `DNSTT / VayDNS` 和 `MasterDNS` 配置档类型，其 `VLESS (Xray)` 配置档也没有 `kcp` 网络。DNS 隧道及其链接仅在桌面版中可用。

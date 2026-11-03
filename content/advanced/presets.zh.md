+++
title = "反审查预设"
description = "TLS 分片、TLS 技巧、uTLS、多路复用、ECH 和 TLS 伪装的作用、适用场景与代价，以及预设与配置档设置如何配合。"
weight = 30
toc = true
+++

有些网络会检查每条加密连接的起始部分，以此封锁代理或降低其速度。Throne 提供了多个选项，用来改变连接起始部分的特征。本页介绍每个选项的作用、适用场景以及代价。

在简洁模式下，`预设设置`（Preset Settings）、配置档编辑器中的 `分片`（Fragment）、`启用 TLS 记录分片`（Enable TLS Record Fragment）和 `TLS 技巧`（TLS Tricks）选项、编辑器的 `高级设置`（Advanced Settings）按钮，以及 `多路复用` 的 `Custom` 选项都会被隐藏（仅在高级模式下显示，参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）。被隐藏的设置会保留其值，并且仍然生效。

## 预设与配置档设置 {#presets}

全局默认值位于 `设置`（Settings）→ `预设设置`（Preset Settings）：

| 标签页 | 选项（默认值） |
| --- | --- |
| `多路复用`（Multiplex） | sing-box：`Protocol`（默认 `smux`）、`Mode`（默认 `Max Streams`）、`Max Streams`（默认 8）、`Max Connections`（默认 4）、`Min Streams`（默认 4）、`Padding`（默认关闭）、`TCP Brutal`（默认关闭）、`默认开启`（Default On，默认关闭）。Xray：`Concurrency`（默认 8）、`Max Connections`（默认为空，即不限制）、`XUDP Concurrency`（默认 16）、`UDP/443`（默认 `Reject`）、`默认复用开启`（Default Mux On，默认关闭）。 |
| `TLS` | `TLS 分片`（TLS Fragment）：`实现`（Implementation，默认 `built-in`）、`大小`（Size，默认 `10-100`）、`睡眠`（Sleep，默认 `2-5`）、`默认开启`（默认关闭）。`TLS 技巧`（TLS Tricks）：`默认开启`（默认关闭）。`uTLS`：`默认指纹`（Default Fingerprint，默认为空）。`TLS 伪装`（TLS Spoof）：`伪装 SNI`（Spoof SNI，默认为空）、`方法`（Method，默认为空）、`默认开启`（默认关闭）。 |
| `HTTP/2 和 QUIC`（HTTP/2 & QUIC） | 用于基于 QUIC 的配置档的调优参数。留空的字段使用核心的默认值。 |

每个配置档都可以在其编辑器中覆盖预设。分片、TLS 技巧、多路复用和 TLS 伪装提供三种选择：

| 配置档中的选择 | 结果 |
| --- | --- |
| `保持默认`（Keep Default） | 跟随预设中的 `默认开启`。新配置档使用此项。 |
| `开启`（On） | 对此配置档始终开启。 |
| `关闭`（Off） | 对此配置档始终关闭。 |

因此，`默认开启` 只会为保持在 `保持默认` 的配置档开启相应选项。自 1.4.0 起，`多路复用` 还有第四个选项 `Custom`，用于配置档自己的设置。参见[多路复用](#multiplex)。

TLS 分片、TLS 技巧、TLS 伪装以及 sing-box 的多路复用预设只适用于运行在 sing-box 上的配置档。`VLESS (Xray)` 配置档有自己的多路复用选项（`保持默认`、`启用`（Enabled）、`禁用`（Disabled），以及自 1.4.0 起的 `Custom`），它跟随 Xray 预设和 `默认复用开启`。uTLS 的 `默认指纹` 对两个核心都适用。参见 [sing-box 与 Xray](@/advanced/xray.zh.md#two-cores)。

## 一次只改一项 {#one-at-a-time}

每个选项都针对一种特定的封锁手段。没有哪个选项在所有地方都有效，而且每个选项都可能让原本正常的配置档失效。

1. 选一个无法连接或速度很慢的配置档。
2. 在它的编辑器中开启一个选项。
3. 测试该配置档：先运行 URL 测试，再打开几个网站。
4. 如果没有改善，就把该选项改回 `保持默认`，然后尝试下一个。
5. 当某个选项对你的大多数配置档都有帮助时，在 `预设设置` 中开启它的 `默认开启`。

多路复用及其填充、TCP Brutal 和 ECH 还需要服务器端的支持。测试配置档的方法请参见[测试与自动选择器](@/guides/testing.zh.md#url-test)。

## TLS 分片 {#tls-fragment}

审查系统常常会读取 TLS 连接第一条消息（即 ClientHello）中的服务器名称（SNI）。TLS 分片会把这条消息拆成小块，使简单的过滤器无法从单个数据包中读出服务器名称。它只对这类简单的过滤器有效。

在配置档编辑器的 `TLS 伪装设置`（TLS Camouflage Settings）下，将 `分片`（Fragment）设为 `保持默认`、`开启` 或 `关闭`。`预设设置` → `TLS` → `TLS 分片` → `实现` 决定拆分方式：

| `实现` | 工作方式 | 设置 |
| --- | --- | --- |
| `built-in`（默认） | 由 sing-box 把 ClientHello 拆分为多个 TCP 数据包。 | 配置档中的 `回退延时`（Fallback Delay），例如 `500ms`：系统无法测得等待时间时，各分片之间的等待时间。 |
| `custom` | 以随机大小的分片发送 ClientHello，分片之间有短暂停顿。 | `大小`：每个分片的字节数，以范围表示（`10-100`）。`睡眠`：分片之间间隔的毫秒数，以范围表示（`2-5`）。 |

编辑器同一区域中的 `启用 TLS 记录分片`（Enable TLS Record Fragment）是一个独立且更轻量的选项。它把 ClientHello 拆分为多个 TLS 记录，而不是多个数据包。请先尝试它，再尝试 `分片`。

**代价：** 新连接的建立会变慢。使用 `custom` 实现时，该配置档的 TCP Fast Open 会被关闭。Naive 配置档只支持 `custom` 实现。

自 1.4.0 起，路由规则也可以对其匹配的连接进行分片，例如直连访问某个被过滤器按名称拦截的网站。请使用 `route` 或 `route-options` 动作的高级规则，并设置 `tls_fragment`（如有需要，再加上 `tls_fragment_fallback_delay`）或 `tls_record_fragment`。参见[路由](@/guides/routing.zh.md#advanced-rules)。

## TLS 技巧 {#tls-tricks}

TLS 技巧会以大小写混合的形式写出服务器名称，例如用 `ExAmPlE.com` 代替 `example.com`。服务器通常不区分大小写，但按精确名称匹配的过滤器可能会漏掉它。

在配置档中（`TLS 伪装设置` 下）将 `TLS 技巧`（TLS Tricks）设为 `开启`；或者勾选 `预设设置` → `TLS` → `TLS 技巧` → `默认开启`，对所有保持在 `保持默认` 的配置档生效。

**代价：** 几乎没有。但对不区分大小写的过滤器无效。

## uTLS 指纹 {#utls}

每个 TLS 客户端都有其典型指纹，即其 ClientHello 中选用了哪些选项以及它们的顺序。普通代理客户端的指纹很容易被识别。uTLS 则会模仿常见浏览器或系统的指纹。

- **按配置档：** 配置档编辑器中的 `指纹`（Fingerprint）。分享链接常用 `fp=` 设置它。
- **全局：** `预设设置` → `TLS` → `uTLS` → `默认指纹`。没有设置自己指纹的配置档会使用它，两个核心均适用（`VLESS (Xray)` 配置档自 1.4.0 起）。当它为空（默认）时，未设置指纹的 sing-box 配置档不使用 uTLS，未设置指纹的 Xray 配置档使用 `chrome`。Hysteria、TUIC、Juicity 和 Naive 配置档不使用 uTLS。
- **可选值：** `chrome`、`firefox`、`edge`、`safari`、`360`、`qq`、`ios`、`android`、`random`、`randomized`，以及自 1.4.0 起提供的 `unsafe`。
- **`unsafe`** 会为该配置档关闭 uTLS，即使设置了 `默认指纹` 也是如此。此时核心使用它自己的 TLS 客户端。只有在这种情况下，配置档的 `Cipher Suites` 字段（自 1.4.0 起）才会生效：填写 TLS 1.2 密码套件名称，以冒号分隔。除非你的服务器需要，否则请留空。

未设置任何指纹的 sing-box REALITY 配置档使用 `random`。

**代价：** 很小。如果某个服务器在使用某个指纹时无法工作，请换一个指纹或清空该字段。

## 多路复用 {#multiplex}

多路复用（mux）会在与服务器之间的一条连接中承载多条连接。这样打开网页时需要新建的连接和握手更少，观察者看到的连接数也更少。

- **按配置档：** 配置档编辑器中的 `多路复用`：`保持默认`、`开启`、`关闭`，或自 1.4.0 起的 `Custom`（`VLESS (Xray)` 配置档为 `保持默认`、`启用`、`禁用` 或 `Custom`）。`保持默认` 和 `开启` 使用预设。选择 `Custom` 后，点击列表旁的 `Configure`：`Custom Multiplex` 窗口（Xray 为 `Custom Mux`）包含与预设相同的字段，并以预设的值作为初始值。来自链接或订阅、并带有自己的 mux 设置的配置档，打开时显示为 `Custom`。
- **sing-box 预设**（`预设设置` → `多路复用` → `Sing-box`）：
  - `Protocol`：`smux`、`yamux` 或 `h2mux`。
  - `Mode`：选择 `Max Streams` 时，一条连接会被重复使用，直到承载 `Max Streams` 个流，然后再打开另一条连接。选择 `Max Connections` 时，Throne 最多保持 `Max Connections` 条连接，并且只有当最空闲的连接已承载 `Min Streams` 个流时，才会打开新连接。
  - `Padding`：为多路复用的流量添加填充。
  - `TCP Brutal`、`Upload (Mb/s)` 和 `Download (Mb/s)`：参见 [TCP Brutal](#tcp-brutal)。
  - `默认开启`。
- **Xray 预设**（`预设设置` → `多路复用` → `Xray`）：
  - `Concurrency`：一条连接在打开另一条连接之前承载的流数。`0` 表示使用 Xray 的默认值 8，`-1` 表示让 TCP 绕过 mux。
  - `Max Connections`（自 1.4.0 起）：最多保持这么多条连接，TCP 和 XUDP 分别计算。当每条连接都承载了 `Concurrency` 个流之后，新的流会加入最空闲的连接。留空表示不限制。
  - `XUDP Concurrency`（自 1.4.0 起）：每条连接的 UDP 流数，UDP 使用单独的连接。`0` 表示通过 TCP 连接承载 UDP，`-1` 表示让 UDP 绕过 mux。
  - `UDP/443`（自 1.4.0 起）：发往 443 端口的 UDP，主要是 QUIC。`Reject`（Xray 的默认值）会让浏览器回退到 TCP，`Allow` 通过 mux 承载它，`Skip` 让它绕过 mux。
  - `默认复用开启`。

在 1.4.0 之前，sing-box 预设只有 `协议`（Protocol）、`并发数`（Concurrency，现为 `Max Streams`）、`Padding` 和 `默认开启`，并且预设中的 `Padding` 会作用于每个启用多路复用的配置档。现在，`Custom` 配置档使用自己的 `Padding`。

服务器必须支持相同协议的 mux。填充同样需要服务器支持。对于使用 `xtls-rprx-vision` 流控（flow）的 VLESS 配置档，配置档编辑器会禁用多路复用。XDRIVE 配置档需要 mux，参见 [XDRIVE](@/advanced/xray.zh.md#xdrive)。

### TCP Brutal {#tcp-brutal}

`TCP Brutal` 会让多路复用连接以你在 `Upload (Mb/s)` 和 `Download (Mb/s)` 中填写的固定速度发送数据，即使发生丢包也是如此。它需要支持 TCP Brutal 的服务器。启用 TCP Brutal 后，所有流共享一条连接，因此 `Mode` 及其数值不再生效。

自 1.4.0 起，TCP Brutal 属于 sing-box 的多路复用设置：在 `预设设置` 中勾选它，作用于所有使用预设的配置档；或者在某个配置档的 `Custom Multiplex` 窗口中勾选。请填写两个速度；缺少的速度会按 1 Mb/s 发送。在 1.3.2 及更早版本中，配置档编辑器中有 `启用 TCP Brutal`（Enable TCP Brutal）、`Brutal 下载速度`（Brutal Download Speed）和 `Brutal 上传速度`（Brutal Upload Speed）。如果配置档带有自己的 TCP Brutal 设置，但其 `多路复用` 不是 `Custom`，则不会使用 TCP Brutal，Throne 会给出警告“TCP Brutal only works with a custom multiplex. Set Multiplex to Custom.”（TCP Brutal 只适用于自定义多路复用，请把多路复用设为 Custom）。

### mux 何时有害 {#mux-costs}

- 所有流量共享一条连接。如果这条连接卡住，其上的所有网页和下载都会卡住。
- 开启 mux 时，大量并行连接（例如来自 BT 客户端的连接）可能导致 Throne 占用大量 CPU（[#1090](https://github.com/throneproj/Throne/issues/1090)）。请为该配置档关闭多路复用。
- 通过新 mux 连接发出的第一个请求耗时更长。如果延迟测试的超时时间较短，可能会把可用的配置档报告为失败。

## ECH {#ech}

Encrypted Client Hello（ECH）会加密 ClientHello 中的服务器名称，使观察者无法读取。它只有在服务器支持 ECH 时才有效。ECH 没有预设，需要按配置档逐个开启。

1. 打开配置档，点击 `高级设置`（Advanced Settings）。
2. 勾选 `启用 ECH`（Enable ECH）。
3. 可选：点击 `ECH 配置`（ECH Config），粘贴服务器的 ECH 配置。如果保持为 `未设置`（Not Set），核心会通过 DNS 查询该配置。
4. 可选：如果用于查询配置的域名不是服务器名称，请在 `ECH Query Name / Resolver` 中填写该域名。自 1.4.0 起，你可以在 `+` 之后添加一个 DNS 服务器，例如 `cloudflare-ech.com+https://1.1.1.1/dns-query`。Throne 随后会向该服务器查询。
5. 点击 `确定`（OK），然后在配置档编辑器中再点击 `确定`。

自 1.4.0 起，如果该字段中没有 DNS 服务器，Throne 会使用直连 DNS 查询该配置。此前，这一查询会经过正需要该配置的代理，因此可能失败。

自 1.4.0 起，Xray 配置档有自己的字段：配置档编辑器 TLS 设置中的 `ECH Config List`。它接受 Base64 格式的服务器 ECH 配置，或者一个域名加一个 DNS-over-HTTPS 服务器，例如 `cloudflare-ech.com+https://8.8.8.8/dns-query`，这样 Xray 会自行查询。分享链接可以通过 `ech=` 参数携带 ECH；参见[链接中的 ECH 与证书固定](@/reference/protocols.zh.md#ech-links)。

**代价：** 如果服务器不支持 ECH，或找不到 ECH 配置，该配置档将无法连接。

## TLS 伪装 {#tls-spoof}

有些过滤器只放行指向少数允许名称的连接，并封锁其余连接。TLS 伪装会在真实的 ClientHello 之前发送一个带有允许名称的伪造 ClientHello。这条伪造消息被故意做成无效的，因此服务器会忽略它，而过滤器会读取它并放行该连接。

全局设置位于 `预设设置` → `TLS` → `TLS 伪装`：

- `伪装 SNI`：放入伪造消息中的允许名称。
- `方法`：使伪造消息无效的方式，可选 `wrong-sequence`、`wrong-checksum`、`wrong-ack`、`wrong-md5` 或 `wrong-timestamp`。留空则使用核心的默认值。
- `默认开启`：对所有保持在 `保持默认` 的配置档启用伪装。只有填写了 `伪装 SNI` 之后才能勾选。

要按配置档设置，请打开 `高级设置`，设置 `TLS 伪装`（`保持默认`、`开启` 或 `关闭`）、`伪装 SNI` 和 `方法`。留空的字段使用预设值。设置了自己的 `伪装 SNI` 的配置档即使处于 `保持默认` 也会进行伪装。

若只想对部分网站进行伪装，请使用带有 `route` 或 `route-options` 动作的高级路由规则，并设置 `tls_spoof` 和 `tls_spoof_method`。参见[路由](@/guides/routing.zh.md#advanced-rules)。

TLS 伪装需要发送原始数据包，因此核心需要更高的权限：

- **Windows：** Throne 必须以管理员身份运行，以便使用 WinDivert 驱动。TLS 伪装在 Windows ARM64 上不可用。
- **Linux：** 核心需要 root 权限，或者 `CAP_NET_RAW` 和 `CAP_NET_ADMIN` 能力（capabilities）。
- **macOS：** 核心需要 root 权限。`wrong-timestamp` 方法在 macOS 上不起作用。

为 TUN 模式进行的权限设置会赋予核心这些权限。参见 [TUN 模式](@/guides/tun_mode.zh.md#privileges)。

**代价：** 需要更高的权限，而且只对放行部分名称的过滤器有效。

## HTTP/2 与 QUIC 调优 {#http2-quic}

`预设设置` → `HTTP/2 和 QUIC` 为 Hysteria、TUIC 和 MASQUE 配置档设置默认值：`空闲超时`（Idle Timeout）、`保活周期`（Keep Alive Period）、`流接收窗口`（Stream Receive Window）、`连接接收窗口`（Connection Receive Window）、`最大并发流数`（Max Concurrent Streams）、`初始数据包大小`（Initial Packet Size）和 `禁用路径 MTU 发现`（Disable Path MTU Discovery）。留空的字段使用核心的默认值。配置档可以在 `高级设置` → `QUIC 参数`（QUIC Parameters）中覆盖它们。

除非服务器的文档要求使用其他值，否则请将这些字段留空。

## Android 版 {#android}

- 同样的预设位于 `设置`（Settings）→ `预设`（Presets）：多路复用（sing-box 和 Xray）、TLS Client Hello 分片、TLS 技巧、uTLS，以及 HTTP/2 和 QUIC。其中 `默认开启` 在 Android 上的英文名称为 `On by default`。
- 配置档同样有 `保持默认`（Keep default）选项，ECH 也是按配置档设置。
- 自 2.0.4 起，多路复用预设具有与桌面版相同的字段；配置档编辑器中的 `启用多路复用`（Enable multiplexer）也提供 `自定义`（Custom）选项，选择后会在其下方直接显示多路复用的各个字段。
- 自 2.0.3 起，路由规则也可以对其匹配的连接进行分片，选项为 `TLS 分片`（TLS fragment）、`TLS 分片回退延迟`（TLS fragment fallback delay）和 `TLS 记录分片`（TLS record fragment）。Throne for Android 2.0.2 没有这些选项。
- TLS 伪装在 Android 上不可用。

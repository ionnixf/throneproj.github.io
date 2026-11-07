+++
title = "反审查预设"
description = "TLS 分片、TLS 技巧、uTLS、多路复用、ECH 和 TLS 伪装的作用、适用场景与代价。"
weight = 30
toc = true
+++

有些网络会检查每条加密连接的起始部分，以此封锁代理或降低其速度。这些选项用来改变连接起始部分的特征。`预设设置`（Preset Settings）、配置档编辑器中的 `TLS 伪装设置`（TLS Camouflage Settings）和 `高级设置`（Advanced Settings），以及 `多路复用` 的 `Custom` 选项仅在高级模式下可用（参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）。

## 预设与配置档设置 {#presets}

全局默认值位于 `设置`（Settings）→ `预设设置`。在配置档编辑器中，分片、TLS 技巧、多路复用和 TLS 伪装提供 `保持默认`（Keep Default，跟随预设中的 `默认开启`（Default On），新配置档使用此项）、`开启`（On）和 `关闭`（Off），因此 `默认开启` 只影响保持在 `保持默认` 的配置档。

预设只适用于运行在 sing-box 上的配置档，但 uTLS 的 `默认指纹`（Default Fingerprint）对两个核心都适用，Xray 的多路复用预设则供 `VLESS (Xray)` 配置档使用。参见 [sing-box 与 Xray](@/advanced/xray.zh.md#two-cores)。

## 一次只改一项 {#one-at-a-time}

每个选项都针对一种封锁手段。没有哪个选项在所有地方都有效，而且每个选项都可能让原本正常的配置档失效。

1. 在一个无法连接或速度很慢的配置档中开启一个选项。
2. 运行 [URL 测试](@/guides/testing.zh.md#url-test)，再打开几个网站。如果没有改善，就把该选项改回 `保持默认`，然后尝试下一个。
3. 当某个选项对你的大多数配置档都有帮助时，在 `预设设置` 中开启它的 `默认开启`。

## TLS 分片 {#tls-fragment}

审查系统常常会读取 TLS 连接第一条消息（即 ClientHello）中的服务器名称（SNI）。TLS 分片会拆分这条消息，使简单的过滤器无法从单个数据包中读出服务器名称。它只对这类简单的过滤器有效。

在配置档编辑器的 `TLS 伪装设置` 下设置 `分片`（Fragment）。请先尝试旁边的 `启用 TLS 记录分片`（Enable TLS Record Fragment）：它更轻量，把 ClientHello 拆分为多个 TLS 记录，而不是多个数据包。

`预设设置` → `TLS` → `TLS 分片`（TLS Fragment）→ `实现`（Implementation）决定拆分方式：

- `built-in`（默认）：由 sing-box 把 ClientHello 拆分为多个 TCP 数据包。配置档中的 `回退延时`（Fallback Delay），例如 `500ms`，是系统无法测得等待时间时各分片之间的等待时间。
- `custom`：随机 `大小`（Size，字节，`10-100`）的分片，分片之间有 `睡眠`（Sleep，毫秒，`2-5`）停顿。Naive 配置档只支持这种实现。

**代价：** 新连接的建立会变慢，而且 `custom` 会关闭该配置档的 TCP Fast Open。

路由规则也可以对其匹配的连接进行分片，例如直连访问某个被过滤器按名称拦截的网站。参见[高级规则](@/guides/routing.zh.md#advanced-rules)中的 `tls_fragment`。

## TLS 技巧 {#tls-tricks}

TLS 技巧会以大小写混合的形式写出服务器名称，例如 `ExAmPlE.com`。服务器不区分大小写，但按精确名称匹配的过滤器可能会漏掉它。在配置档编辑器的 `TLS 伪装设置` 下设置 `TLS 技巧`（TLS Tricks）。

**代价：** 几乎没有，但对不区分大小写的过滤器无效。

## uTLS 指纹 {#utls}

uTLS 会让 ClientHello 看起来像常见浏览器或系统发出的，而不是像容易通过指纹识别出来的普通代理客户端。

- **按配置档：** 配置档编辑器中的 `指纹`（Fingerprint）。分享链接常用 `fp=` 设置它。
- **全局：** `预设设置` → `TLS` → `uTLS` → `默认指纹`，用于没有设置自己指纹的配置档。当它为空（默认）时，这些配置档在 sing-box 上不使用 uTLS，在 sing-box REALITY 上使用 `random`，在 Xray 上使用 `chrome`。Hysteria、TUIC、Juicity 和 Naive 配置档不使用 uTLS。
- **可选值：** `chrome`、`firefox`、`edge`、`safari`、`360`、`qq`、`ios`、`android`、`random`、`randomized` 和 `unsafe`。`unsafe` 会关闭 uTLS，即使设置了 `默认指纹` 也是如此；只有这时配置档的 `Cipher Suites` 字段才会生效（TLS 1.2 密码套件，以冒号分隔；除非你的服务器需要，否则请留空）。

**代价：** 很小。如果某个服务器在使用某个指纹时无法工作，请换一个指纹或清空该字段。

## 多路复用 {#multiplex}

多路复用（mux）会在与服务器之间的一条连接中承载多条连接，这样网页需要的握手更少，观察者看到的连接数也更少。服务器必须支持相同协议的 mux，如果使用填充（padding），也必须支持填充。

在配置档编辑器中设置 `多路复用`（Multiplex）（`VLESS (Xray)` 配置档用 `启用`（Enabled）和 `禁用`（Disabled）代替 `开启` 和 `关闭`）。`Custom` → `Configure` 会为该配置档提供一份自己的预设字段；来自链接或订阅、并带有自己的 mux 设置的配置档打开时显示为 `Custom`。使用 `xtls-rprx-vision` 流控（flow）的 VLESS 配置档会禁用多路复用，而 [XDRIVE](@/advanced/xray.zh.md#xdrive) 必须使用它。

sing-box 预设（`预设设置` → `多路复用` → `Sing-box`）：

- `Protocol`：`smux`、`yamux` 或 `h2mux`。
- `Mode`：选择 `Max Streams` 时，一条连接承载这么多个流后就打开另一条连接。选择 `Max Connections` 时，最多保持这么多条连接，并且只有当最空闲的连接已承载 `Min Streams` 个流时才打开新连接。
- `Padding`：为多路复用的流量添加填充。
- `TCP Brutal`：以你在 `Upload (Mb/s)` 和 `Download (Mb/s)` 中填写的固定速度发送，即使发生丢包也是如此。两个速度都要填写。它需要服务器支持，而且所有流会共享一条连接。

Xray 预设（`预设设置` → `多路复用` → `Xray`）：

- `Concurrency`：一条连接在打开另一条连接之前承载的流数。`0` 表示使用 Xray 的默认值 8，`-1` 表示让 TCP 绕过 mux。
- `Max Connections`：最多保持这么多条连接，TCP 和 XUDP 分别计算。留空表示不限制。
- `XUDP Concurrency`：每条连接的 UDP 流数，UDP 使用单独的连接。`0` 表示通过 TCP 连接承载 UDP，`-1` 表示让 UDP 绕过 mux。
- `UDP/443`，主要是 QUIC：`Reject`（默认）会让浏览器回退到 TCP，`Allow` 通过 mux 承载它，`Skip` 让它绕过 mux。

**代价：** 同一条连接上的所有内容会一起卡住。大量并行连接（例如来自 BT 客户端的连接）可能导致 Throne 占用大量 CPU（[#1090](https://github.com/throneproj/Throne/issues/1090)），请为该配置档关闭多路复用。通过新 mux 连接发出的第一个请求更慢，因此延迟测试的超时时间较短时，可能会把可用的配置档报告为失败。

## ECH {#ech}

Encrypted Client Hello（ECH）会加密服务器名称，使观察者无法读取。它只有在服务器支持时才有效，并且需要按配置档逐个设置：

1. 打开配置档，点击 `高级设置` 并勾选 `启用 ECH`（Enable ECH）。
2. 可选：在 `ECH 配置`（ECH Config）中粘贴服务器的 ECH 配置。如果保持为 `未设置`（Not Set），会通过 DNS 查询该配置，查询的域名为 `ECH Query Name / Resolver` 中填写的域名，否则为服务器名称。要改用某个 DNS 服务器而不是直连 DNS 查询，请把它加在 `+` 之后，例如 `cloudflare-ech.com+https://1.1.1.1/dns-query`。
3. 在两个窗口中都点击 `确定`（OK）。

Xray 配置档则使用其 TLS 设置中的 `ECH Config List`：Base64 格式的 ECH 配置，或者一个域名加一个 DNS-over-HTTPS 服务器，例如 `cloudflare-ech.com+https://8.8.8.8/dns-query`。分享链接通过 `ech=` 携带 ECH，参见[链接中的 ECH 与证书固定](@/reference/protocols.zh.md#ech-links)。

**代价：** 如果服务器不支持 ECH，或找不到 ECH 配置，该配置档将无法连接。

## TLS 伪装 {#tls-spoof}

有些过滤器只放行少数允许的名称，并封锁其余连接。TLS 伪装会在真实的 ClientHello 之前发送一个带有允许名称的伪造 ClientHello。这条伪造消息被故意做成无效的，因此服务器会忽略它，而过滤器会放行该连接。

- **预设**（`预设设置` → `TLS` → `TLS 伪装`（TLS Spoof））：`伪装 SNI`（Spoof SNI）是允许的名称，`方法`（Method）是使伪造消息无效的方式：`wrong-sequence`、`wrong-checksum`、`wrong-ack`、`wrong-md5` 或 `wrong-timestamp`（留空则使用核心的默认值）。
- **按配置档：** `高级设置` → `TLS 伪装`、`伪装 SNI` 和 `方法`。留空的字段使用预设值。设置了自己的 `伪装 SNI` 的配置档即使处于 `保持默认` 也会进行伪装。
- **按网站：** 带有 `tls_spoof` 和 `tls_spoof_method` 的高级路由规则，参见[高级规则](@/guides/routing.zh.md#advanced-rules)。

TLS 伪装需要发送原始数据包，因此核心需要 [TUN 模式](@/guides/tun_mode.zh.md#privileges)所设置的权限：Windows 上为管理员权限（Windows ARM64 不可用），Linux 上为 root 或 `CAP_NET_RAW` 和 `CAP_NET_ADMIN`，macOS 上为 root，且 `wrong-timestamp` 在 macOS 上不起作用。

**代价：** 需要更高的权限，而且只对放行部分名称的过滤器有效。

## HTTP/2 与 QUIC 调优 {#http2-quic}

`预设设置` → `HTTP/2 和 QUIC`（HTTP/2 & QUIC）为 Hysteria、TUIC 和 MASQUE 配置档设置 `空闲超时`（Idle Timeout）、`保活周期`（Keep Alive Period）和 `初始数据包大小`（Initial Packet Size）等默认值。配置档可以在 `高级设置` → `QUIC 参数`（QUIC Parameters）中覆盖它们。除非服务器的文档要求使用其他值，否则请将这些字段留空，以使用核心的默认值。

## Android 版 {#android}

- 同样的预设位于 `设置`（Settings）→ `预设`（Presets），其中 `默认开启` 叫作 `On by default`。ECH 按配置档设置。
- 配置档编辑器中的 `启用多路复用`（Enable multiplexer）也提供 `自定义`（Custom）选项，选择后会在其下方直接显示多路复用的各个字段。
- 路由规则可以对其匹配的连接进行分片，选项为 `TLS 分片`（TLS fragment）、`TLS 分片回退延迟`（TLS fragment fallback delay）和 `TLS 记录分片`（TLS record fragment）。
- TLS 伪装不可用。

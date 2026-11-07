+++
title = "sing-box 与 Xray"
description = "哪个核心运行你的配置档、Xray VLESS 首选项如何起作用，以及如何修复较新 Xray 服务器导致的 Reality 错误。"
weight = 10
toc = true
+++

大多数配置档运行在 sing-box 上，部分 VLESS 配置档和所有 Xray 自定义配置运行在 Xray 上，Throne 同样内置了 Xray。在简洁模式下，`核心`（Core）和 `日志`（Logging）标签页以及本页的大多数其他设置都会被隐藏，参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)。

## 合二为一的两个核心 {#two-cores}

sing-box 始终在运行，负责本地代理端口、TUN 模式、路由和 DNS。Xray 作为辅助组件，只有当正在运行的配置档、其代理链中的某个跳点或某条路由规则的出站需要它时才会启动。sing-box 在内部把这部分流量交给 Xray，因此 TUN 模式、路由配置档和 DNS 设置在两个核心下的效果相同。

`类型`（Type）列显示为 `VLESS (Xray)`、`Custom Xray … Outbound` 或 `Custom Xray Config` 的配置档运行在 Xray 上；其他所有配置档（包括 `VLESS`）都运行在 sing-box 上。使用了 Xray 独有功能的 `vless://` 链接总会被导入为 `VLESS (Xray)`，无论 [Xray VLESS 首选项](#vless-preference)如何设置：

- XHTTP 传输（`type=xhttp`）及其 `extra` 设置；
- VLESS 加密（`encryption` 的值不是 `none`）；
- Finalmask；
- mKCP 传输（`type=kcp`）；
- 带 HTTP 头部并同时启用 TLS 的 TCP（`raw`）传输。

mKCP 通过 UDP 承载连接（在配置档编辑器的 `传输(网络)`（Network）中选择 `kcp`）。在最终掩码（Finalmask）中加上 `xdns` 掩码后，它就成为 DNS 隧道 [XDNS](@/advanced/dns_tunnels.zh.md#xdns)。[XDRIVE](#xdrive) 同样只有 Xray 支持，但它没有链接格式。

要查看 Throne 为某个配置档生成的配置，请右键点击它 → `分享`（Share）→ `导出配置`（Export config）。Xray 的消息显示在 `日志`（Logs）标签页中；其详细程度在 `设置`（Settings）→ `基本设置`（Basic Settings）→ `日志`（Logging）→ `Xray 日志级别`（Xray Log level）中设置。

## 为 VLESS 链接选择核心 {#vless-preference}

`设置` → `基本设置` → `核心`（Core）→ `Xray VLESS 首选项`（Xray VLESS Preference）决定导入 `vless://` 链接时使用哪个核心：

| 选项 | 导入为 `VLESS (Xray)` 的链接 |
| --- | --- |
| `XHTTP Only` | 仅限需要 Xray 独有功能的链接（见上文）。Reality 链接仍使用 sing-box。 |
| `XHTTP And Reality`（默认） | 上述链接，外加所有 Reality 链接。 |
| `All VLESS` | 所有 `vless://` 链接。 |

其他来源：

- **Clash / Mihomo YAML：** VLESS 条目在使用 XHTTP、VLESS 加密，或基于 TCP、WebSocket 或 gRPC 的 Reality 时会使用 Xray（首选项为 `XHTTP Only` 时除外）。其他 VLESS 条目仍使用 sing-box，即使首选项为 `All VLESS` 也是如此。
- **Xray JSON：** 每个 VLESS 出站都会成为 `VLESS (Xray)`。
- **sing-box JSON：** VLESS 出站仍使用 sing-box。
- **配置档编辑器：** 由你自己在 `类型` 中选择 `VLESS` 或 `VLESS (Xray)`。

{% alert_info() %}
更新 Throne 会保留你已保存的首选项。如果你从 1.2.0 之前就开始使用 Throne，你的设置可能仍是 `XHTTP Only`。
{% end %}

### 更改首选项之后 {#after-change}

该首选项只影响更改之后导入的配置档。要把已有的配置档换到另一个核心：

- **订阅分组：** 更新订阅（`分组`（Groups）→ `更新订阅`（Update subscription））。Throne 会用新配置档替换受影响的配置档，因此它们原有的测试结果会丢失。
- **从链接添加的配置档：** 重新添加该链接，然后删除旧配置档。
- **手动创建的配置档：** 其类型无法更改。请用另一种类型新建一个配置档。

如果在分组的订阅设置中开启了 `保留工作配置档`（Keep working profiles），旧的可用配置档可能会与新配置档并存。请自行删除它们。

## 较新 Xray 服务器的 Reality 错误 {#reality}

较新的 Xray 服务器会拒绝 sing-box 的 Reality 客户端。配置档无法连接，日志中显示 `reality verification failed`（[#1780](https://github.com/throneproj/Throne/issues/1780)）：

- Xray 26.7.11 至 26.7.28 只接受报告较新 Xray 版本的客户端。
- Xray 26.9.9 及更新版本要求使用 sing-box 的 Reality 客户端不提供的密钥交换（X25519MLKEM768）。

改为让该配置档运行在 Xray 上：

1. 打开 `设置` → `基本设置` → `核心`。
2. 将 `Xray VLESS 首选项` 设为 `XHTTP And Reality` 或 `All VLESS`，然后点击 `确定`（OK）。
3. 更新订阅，或重新添加链接（见[更改首选项之后](#after-change)）。
4. 确认 `类型` 列现在显示为 `VLESS (Xray)`。

对于 Clash / Mihomo YAML 订阅，只有基于 TCP、WebSocket 或 gRPC 的 Reality 条目会改用 Xray。对于其他传输方式，如果你的服务商以链接列表的形式提供订阅，请改用该格式。

如果你自己运行的是 26.7.11 至 26.7.28 版本的 Xray 服务器，也可以改为在其 VLESS 入站的 `realitySettings` 中添加 `"minClientVer": "0.0.0"`，以允许较旧的客户端连接。此方法对 26.9.9 及更新版本无效。

## 信任系统证书 {#system-ca}

Xray 只根据其内置的根证书检查服务器证书，因此如果服务器的证书来自你自己安装的根证书，例如公司根证书或自签名根证书，就无法通过检查。勾选 `设置`（Settings）→ `基本设置`（Basic Settings）→ `安全`（Security）→ `Xray 信任系统证书`（Trust System Certificates in Xray）（默认关闭），即可让 Xray 同时信任系统中的证书。

## XDRIVE {#xdrive}

使用 XDRIVE 传输时，Throne 和服务器之间不直接连接，而是以文件的形式，在双方都能访问的存储中交换流量：Google Drive 文件夹、S3 存储桶、其他提供 HTTP API 的存储服务，或者本机上的一个文件夹。只有在服务器已为 XDRIVE 配置好时才使用它，并使用服务器运营者提供的设置。

XDRIVE 没有链接格式，因为链接会带上存储的密钥。请改为导入服务器的 Xray JSON 配置，或者 Throne 链接（`throne://add/…`）。分享 XDRIVE 配置档时会得到它的 Throne 链接。

要在配置档编辑器中设置 XDRIVE，请在 `传输(网络)`（Network）中选择 `xdrive`。XDRIVE 需要以下 VLESS 设置：

- `加密`（Encryption）：服务器提供的 VLESS 加密字符串，以 `mlkem768x25519plus` 开头。
- `流`（Flow）：留空。
- `多路复用`（Multiplex）：除 `禁用`（Disabled）之外的任意值。选择 `保持默认`（Keep Default）时，Throne 会为 XDRIVE 开启 mux。参见[多路复用](@/advanced/presets.zh.md#multiplex)。

在 `存储`（Storage）组中选择 `服务`（Service），然后填写运营者提供的内容：

- `google-drive`（默认）：`文件夹`（Folder）（共享文件夹的 ID）、`客户端 ID`（Client ID）、`客户端密钥`（Client Secret）和 `刷新令牌`（Refresh Token）。
- `s3`，任意兼容 S3 的存储，例如 AWS S3、MinIO 或 Cloudflare R2：`Endpoint`、`Bucket`、`文件夹`（键前缀）、`Access Key ID` 和 `Secret Access Key`，以及可选的 `Region`（留空表示 `us-east-1`）和 `Session Token`。`Path-Style Addressing`（默认开启）把存储桶放在 URL 路径中，而不是主机名中。
- `template`，任意提供 HTTP API 的存储：`模板`（Template）（以 JSON 描述的 API 操作）和 `密钥`（Secrets）（以逗号分隔、以 `{secret0}`、`{secret1}` 等读取的值）。
- `local`：`文件夹`，即本机上的一个文件夹。

`前置地址`（Front Address）和 `前置端口`（Front Port）（默认 443）为可选项：Throne 通过这个地址连接存储 API，而 TLS 中仍然使用 API 的主机名，这是一种域前置（domain fronting）。`调优`（Tuning）组留空即使用 Xray 的默认值。

## 代理链中的 Xray {#xray-in-chains}

代理链可以混用 sing-box 和 Xray 配置档，但有两项限制：

- 所有 Xray 跳点必须相邻：先是 sing-box 跳点，然后是 Xray 跳点，最后是 sing-box 跳点。否则配置档无法启动，日志中显示“Too many core transitions”（核心切换次数过多）。
- `自定义 (Xray 配置)` 配置档必须是最上面的跳点，并且不能与其他 Xray 跳点位于同一条代理链中。

分组的前置代理和落地代理以及 WARP 也算作跳点。参见[代理链与自定义配置](@/advanced/chains.zh.md#chains)。

## Xray geo 文件 {#geo-files}

如果 `自定义 (Xray 配置)` 配置档自己的 `routing` 规则使用了 `geoip:` 或 `geosite:` 值，它就需要 `geoip.dat` 和 `geosite.dat` 这两个文件。缺少这些文件时，Throne 会提供下载：点击 `是`（Yes），然后重新启动该配置档。

在 `设置` → `基本设置` → `杂项`（Miscellaneous）→ `Xray Geo 资源`（Xray Geo Assets）中选择来源：Loyalsoldier（全球和中国，默认）、Chocolate4U（伊朗）、runetfreedom（俄罗斯）或 v2fly。两个文件请从同一来源获取。如果 Throne 显示“Geo 资源缺失类别”（Geo asset missing category），请选择包含该类别的来源，然后点击该文件对应的 `下载`（Download）。

## Android 版 {#android}

- 该首选项位于 `设置`（Settings）→ `核心`（Core）→ `Xray VLESS 偏好`（Xray VLESS preference），选项为 `仅 XHTTP`（XHTTP only）、`XHTTP 与 Reality`（XHTTP and Reality，默认）和 `全部 VLESS`（All VLESS）。
- XDRIVE：在 `传输协议`（Network）中选择 `XDRIVE`。相关字段位于 `XDRIVE 设置`（XDRIVE settings）和 `XDRIVE 调优`（XDRIVE tuning）中，`密钥列表`（Secrets）每行填写一个值。
- mKCP：在 `传输协议` 中选择 `mKCP`。相关字段位于 `mKCP 设置`（mKCP settings）和 `mKCP 调优`（mKCP tuning）中。对于 XDNS，请在 `Finalmask` 中添加 `xdns` 掩码。
- `设置`（Settings）→ `核心`（Core）→ `在 Xray 中信任系统证书`（Trust system certificates in Xray）信任 Android 自带的根证书，但不信任你自己安装的证书。
- Android 版 Throne 会在 Xray 配置需要时自动下载 `geoip.dat` 和 `geosite.dat`。在 `设置`（Settings）→ `核心`（Core）→ `Xray Geo 资源`（Xray geo assets）中选择来源。

+++
title = "sing-box 与 Xray"
description = "哪个核心运行你的配置档、Xray VLESS 首选项如何起作用，以及如何修复较新 Xray 服务器导致的 Reality 错误。"
weight = 10
toc = true
+++

Throne 的核心基于 sing-box 构建，其中也包含 Xray。大多数配置档运行在 sing-box 上，部分 VLESS 配置档和所有 Xray 自定义配置运行在 Xray 上。本页说明 Throne 如何选择核心、如何针对 VLESS 链接更改这一选择，以及如何修复 Reality 连接错误。

在简洁模式下，本页提到的大多数设置都会被隐藏：`基本设置`（Basic Settings）中的 `日志`（Logging）和 `核心`（Core）标签页、`Xray Geo 资源`（Xray Geo Assets）和 `Xray 信任系统证书`（Trust System Certificates in Xray），XDRIVE 和 mKCP 的 `调优`（Tuning）组，以及 `分享`（Share）→ `导出配置`（Export config）。参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)。

## 合二为一的两个核心 {#two-cores}

sing-box 始终在运行，负责本地代理端口、TUN 模式、路由规则和 DNS。Xray 作为辅助组件运行在同一个核心中，只有当正在运行的配置档需要它时才会启动，例如该配置档本身、其代理链中的某个跳点或某条路由规则的出站运行在 Xray 上时。sing-box 通过内部的本地连接把这部分流量交给 Xray，因此 TUN 模式、路由配置档和 DNS 设置在两个核心下的效果相同。

以下配置档运行在 Xray 上：

| `类型`（Type）列 | 来源 |
| --- | --- |
| `VLESS (Xray)` | `vless://` 链接（取决于 [Xray VLESS 首选项](#vless-preference)）；Xray JSON 中的 VLESS 出站；配置档编辑器中的 `VLESS (Xray)` 类型。 |
| `Custom Xray … Outbound` | `自定义 (Xray 出站)`（Custom (Xray outbound)）类型；导入的 Xray JSON 中的其他出站。 |
| `Custom Xray Config` | `自定义 (Xray 配置)`（Custom (Xray config)）类型；导入的完整 Xray 配置列表。 |

其他所有配置档（包括 `VLESS`）都运行在 sing-box 上。

有些 VLESS 功能只有 Xray 支持。使用了其中任一功能的 `vless://` 链接总会被导入为 `VLESS (Xray)`，无论首选项如何设置：

- XHTTP 传输（`type=xhttp`）及其 `extra` 设置；
- VLESS 加密（`encryption` 的值不是 `none`）；
- Finalmask；
- mKCP 传输（`type=kcp`，自 1.4.0 起）；
- 带 HTTP 头部并同时启用 TLS 的 TCP（`raw`）传输。

XDRIVE 传输（自 1.4.0 起）同样只有 Xray 支持，但它没有链接格式。参见 [XDRIVE](#xdrive)。

mKCP 通过 UDP 承载连接。要使用它，请在配置档编辑器的 `传输(网络)`（Network）中选择 `kcp`。在最终掩码（Finalmask）中加上 `xdns` 掩码后，mKCP 就成为 XDNS，一种 DNS 隧道。mKCP 的各字段参见 [XDNS](@/advanced/dns_tunnels.zh.md#xdns)。

Xray 的消息同样显示在 `日志`（Logs）标签页中。其详细程度在 `设置`（Settings）→ `基本设置`（Basic Settings）→ `日志`（Logging）→ `Xray 日志级别`（Xray Log level）中设置。

## 为 VLESS 链接选择核心 {#vless-preference}

`设置` → `基本设置` → `核心`（Core）→ `Xray VLESS 首选项`（Xray VLESS Preference）决定导入 `vless://` 链接时使用哪个核心：

| 选项 | 导入为 `VLESS (Xray)` 的链接 |
| --- | --- |
| `XHTTP Only` | 仅限需要 Xray 独有功能的链接（见上方列表）。Reality 链接仍使用 sing-box。 |
| `XHTTP And Reality`（默认） | 上述链接，外加所有 Reality 链接。 |
| `All VLESS` | 所有 `vless://` 链接。 |

其余 `vless://` 链接都会成为运行在 sing-box 上的 `VLESS` 配置档。

该首选项只对 `vless://` 链接生效：

- **Clash / Mihomo YAML：** VLESS 条目在使用 XHTTP 或 VLESS 加密时会使用 Xray。自 1.4.0 起，基于 TCP、WebSocket 或 gRPC 的 Reality 条目也会使用 Xray，除非首选项为 `XHTTP Only`。其他 VLESS 条目仍使用 sing-box，即使首选项为 `All VLESS` 也是如此。
- **Xray JSON：** 每个 VLESS 出站都会成为 `VLESS (Xray)`。
- **sing-box JSON：** VLESS 出站仍使用 sing-box。
- **配置档编辑器：** 由你自己在 `类型` 列表中选择 `VLESS` 或 `VLESS (Xray)`。

{% alert_info() %}
`XHTTP And Reality` 从 1.2.0 起成为默认值，`All VLESS` 也是在该版本加入的。更新 Throne 会保留你已保存的值。如果你从 1.2.0 之前就开始使用 Throne，你的设置可能仍是 `XHTTP Only`。
{% end %}

### 更改首选项之后 {#after-change}

该首选项只影响更改之后导入的配置档，已有的配置档保持原来的核心。要把它们换到另一个核心：

- **订阅分组：** 更新订阅（`分组`（Groups）→ `更新订阅`（Update subscription），或右键点击分组标签页 → `更新订阅`）。Throne 会用另一种类型的新配置档替换受影响的配置档，因此它们原有的测试结果会丢失。
- **从链接添加的配置档：** 重新添加该链接，然后删除旧配置档。
- **手动创建的配置档：** 其类型无法更改。请用另一种类型新建一个配置档。

如果在分组的 `高级`（Advanced）订阅设置中开启了 `保留工作配置档`（Keep working profiles），旧的可用配置档可能会与新配置档并存。请自行删除它们。

### 配置档使用的是哪个核心？ {#which-core}

查看配置档列表中的 `类型` 列。`VLESS` 运行在 sing-box 上；`VLESS (Xray)`、`Custom Xray … Outbound` 和 `Custom Xray Config` 运行在 Xray 上。

如果想在该列中同时看到安全层（例如 Reality），请开启 `设置` → `基本设置` → `样式`（Style）→ `显示配置安全信息`（Show Config Security）。要查看 Throne 为某个配置档生成的配置，请右键点击它 → `分享`（Share）→ `导出配置`（Export config）（自 1.4.0 起）。窗口中有一个 `sing-box` 标签页，当配置档使用 Xray 时还有一个 `Xray` 标签页。`复制`（Copy）会复制你当前看到的标签页。在 1.3.2 及更早版本中，菜单中是单独的 `导出 Sing-box 配置`（Export Sing-box config）和 `导出 Xray 配置`（Export Xray config）菜单项。

## 较新 Xray 服务器的 Reality 错误 {#reality}

较新的 Xray 服务器会拒绝 sing-box 的 Reality 客户端。此时配置档无法连接，日志中显示 `reality verification failed`（[#1780](https://github.com/throneproj/Throne/issues/1780)）。

- Xray 26.7.11 至 26.7.28 只接受报告较新 Xray 版本的客户端。
- Xray 26.9.9 及更新版本要求使用较新的密钥交换（X25519MLKEM768），而 sing-box 的 Reality 客户端不提供这种密钥交换。

解决方法是让该配置档运行在 Xray 上：

1. 打开 `设置` → `基本设置` → `核心`。
2. 将 `Xray VLESS 首选项` 设为 `XHTTP And Reality` 或 `All VLESS`。
3. 点击 `确定`（OK）。
4. 更新订阅，或重新添加链接（见[更改首选项之后](#after-change)）。
5. 确认 `类型` 列现在显示为 `VLESS (Xray)`。

在 1.3.2 及更早版本中，这种方法对来自 Clash / Mihomo YAML 订阅的 Reality 条目无效，因为它们始终使用 sing-box。自 1.4.0 起，当它们使用 TCP、WebSocket 或 gRPC 时，也会遵循该首选项。对于其他传输方式，如果你的服务商以链接列表的形式提供订阅，请改用该格式。

如果你自己运行的是 26.7.11 至 26.7.28 版本的 Xray 服务器，也可以在其 VLESS 入站的 `realitySettings` 中添加 `"minClientVer": "0.0.0"`，以允许较旧的客户端连接。此方法对 Xray 26.9.9 及更新版本无效。

## 旧的 Xray 设置 {#old-settings}

较新的 Xray 版本会拒绝一些旧设置，使用这些设置的配置档或配置无法启动。自 1.4.0 起，Throne 中的 Xray 重新接受这些设置：

- 连接互联网上的服务器、但不使用 TLS 或其他加密的 VLESS 和 Trojan。较新的 Xray 只允许对私有地址这样做。
- Xray 配置的 TLS 设置中的 `allowInsecure`。
- Xray 配置中旧的 Shadowsocks 加密方式 `aes-128-cfb`、`aes-256-cfb`、`chacha20` 和 `chacha20-ietf`，以及 `none` 或 `plain`。
- Xray 配置中的 VMess 安全类型 `none` 和 `zero`。

只有当 `Xray 日志级别`（Xray Log level）为 `debug` 时，才会显示 Xray 关于已弃用功能的警告。这些设置安全性较低，或者更容易被识别。只对需要它们的服务器使用。

## 信任系统证书 {#system-ca}

Xray 只根据其内置的根证书检查服务器证书。如果服务器的证书来自你自己安装的根证书，例如公司根证书或自签名根证书，就无法通过这项检查。自 1.4.0 起，勾选 `设置`（Settings）→ `基本设置`（Basic Settings）→ `安全`（Security）→ `Xray 信任系统证书`（Trust System Certificates in Xray）（默认关闭），即可让 Xray 同时信任系统中安装的证书。

该选项作用于所有 Xray 配置档的 TLS 设置，以及 `自定义 (Xray 出站)` 和 `自定义 (Xray 配置)` 配置档中每个出站的 TLS 设置，包括 XHTTP 的下载连接。自行设置了 `useSystemCA` 的出站会保留自己的值。sing-box 配置档不使用该选项。

## XDRIVE {#xdrive}

自 1.4.0 起，`VLESS (Xray)` 配置档可以使用 XDRIVE 传输。使用 XDRIVE 时，Throne 和服务器之间不直接连接，而是以文件的形式，在双方都能访问的存储中交换流量：可以是 Google Drive 文件夹、S3 存储桶、其他提供 HTTP API 的存储服务，或者本机上的一个文件夹。只有在服务器已为 XDRIVE 配置好时才使用它，并使用服务器运营者提供的设置。

XDRIVE 没有链接格式，因为链接会带上存储的密钥。请改为导入服务器的 Xray JSON 配置，或者 Throne 链接（`throne://add/…`）。带有 `type=xdrive` 的 `vless://` 链接会被拒绝，并显示“XDRIVE has no share-link format. Import the server's JSON configuration instead.”（XDRIVE 没有分享链接格式，请改为导入服务器的 JSON 配置）。分享 XDRIVE 配置档时，Throne 会提供它的 Throne 链接。

要在配置档编辑器中设置 XDRIVE，请在 `传输(网络)`（Network）中选择 `xdrive`。XDRIVE 只能与以下 VLESS 设置一起使用，设置为其他值时编辑器会显示错误：

- `加密`（Encryption）：服务器提供的 VLESS 加密字符串，以 `mlkem768x25519plus` 开头。
- `流`（Flow）：留空。
- `多路复用`（Multiplex）：`保持默认`（Keep Default）、`启用`（Enabled）或 `Custom`，不能是 `禁用`（Disabled）。选择 `保持默认` 时，即使 `默认复用开启`（Default Mux On）处于关闭状态，Throne 也会为 XDRIVE 开启 mux。Throne 还会忽略会让流量绕过 mux 的设置，例如值为 `-1` 的 `Concurrency`。参见[多路复用](@/advanced/presets.zh.md#multiplex)。

`存储`（Storage）组：

| 字段 | 作用 |
| --- | --- |
| `服务`（Service） | `google-drive`（默认）：Google Drive 文件夹。`s3`：任意兼容 S3 的存储中的存储桶，例如 AWS S3、MinIO 或 Cloudflare R2。`template`：由 `模板`（Template）描述其 HTTP API 的任意存储服务。`local`：本机上的文件夹。 |
| `文件夹`（Folder） | `google-drive`：双方共享的文件夹的 ID。`s3`：双方共享的存储桶内的键前缀。`local`：文件夹路径。`template`：模板中以 `{folder}` 读取的值。`google-drive`、`s3` 和 `local` 必填。 |
| `客户端 ID`（Client ID）、`客户端密钥`（Client Secret）、`刷新令牌`（Refresh Token） | 仅用于 `google-drive`，三项均必填：OAuth 客户端，以及拥有该文件夹的 Google 账户的刷新令牌。 |
| `Endpoint` | 仅用于 `s3`，必填：S3 API 的 URL，例如 `https://s3.us-east-1.amazonaws.com`，或 MinIO、R2 的端点。必须以 `http://` 或 `https://` 开头，且不能包含凭据、查询或片段。 |
| `Region` | 仅用于 `s3`：请求签名所用的区域。留空表示 `us-east-1`。 |
| `Bucket` | 仅用于 `s3`，必填：双方共享的存储桶。 |
| `Path-Style Addressing` | 仅用于 `s3`，默认开启：把存储桶放在 URL 路径中。关闭后，存储桶会成为端点主机名的一部分。这需要使用域名而不是 IP 地址的端点，并且存储桶名称只能由小写字母、数字、点和连字符组成。 |
| `Access Key ID`、`Secret Access Key`、`Session Token` | 仅用于 `s3`：S3 凭据。前两项必填。`Session Token` 可选，用于临时凭据。 |
| `密钥`（Secrets） | 仅用于 `template`：模板中以 `{secret0}`、`{secret1}` 等读取的值，以逗号分隔。 |
| `模板`（Template） | 仅用于 `template`，必填：以 JSON 描述的存储 API 的 auth、put、get、list 和 delete 操作。点击按钮即可编辑。 |
| `前置地址`（Front Address）、`前置端口`（Front Port） | 可选，不适用于 `local`：Throne 通过这个地址连接存储 API，而 TLS 中仍然使用 API 的主机名，这是一种域前置（domain fronting）。`前置端口` 默认为 443，只有与 `前置地址` 一起使用时才生效。 |

`调优`（Tuning）组包含时间和大小相关的设置。字段留空即使用 Xray 的默认值，字段中会显示该默认值：

| 字段 | Xray 默认值 | 设置的内容 |
| --- | --- | --- |
| `分段字节数`（Segment Bytes） | 524288 | 每个文件的最大上传量，单位为字节。Xray 最多允许 16 MiB。 |
| `刷新间隔`（Flush Interval） | 20 | 上传前收集写入数据的时长，单位为毫秒。 |
| `轮询间隔`（Poll Interval） | 50 | 两次检查新文件之间的最短等待时间，单位为毫秒。 |
| `最大轮询间隔`（Max Poll Interval） | 500 | 连接空闲时两次检查之间的最长等待时间，单位为毫秒。 |
| `积极轮询窗口`（Eager Window） | 2000 | 有流量之后，检查保持最短等待时间的时长，单位为毫秒。 |
| `缺段超时`（Hole Timeout） | 30000 | 等待缺失分段的时长，超时后连接失败，单位为毫秒。 |
| `会话存活时间`（Session TTL） | 300 | 空闲会话在其文件被清理之前保留的时长，单位为秒。 |
| `并发数`（Concurrency） | 8 | 每个连接对存储的并行请求数。Xray 最多允许 64。 |

## 代理链中的 Xray {#xray-in-chains}

代理链可以混用 sing-box 和 Xray 配置档，但有两项限制：

- 所有 Xray 跳点必须相邻。流量只能从 sing-box 切换到 Xray 再切换回来一次：先是 sing-box 跳点，然后是 Xray 跳点，最后是 sing-box 跳点。否则配置档无法启动，日志中显示“Too many core transitions”（核心切换次数过多）。
- `自定义 (Xray 配置)` 配置档必须是最上面的跳点，并且不能与其他 Xray 跳点位于同一条代理链中，因为一条代理链只能使用一个 Xray 实例。

分组的前置代理和落地代理以及 WARP 也算作跳点。参见[代理链与自定义配置](@/advanced/chains.zh.md#chains)。

## Xray geo 文件 {#geo-files}

`自定义 (Xray 配置)` 配置档可以带有自己的 `routing` 规则，并在其中使用 `geoip:` 或 `geosite:` 值。为此，Xray 需要 `geoip.dat` 和 `geosite.dat` 这两个文件。其他配置档以及 Throne 的路由配置档不使用这些文件，而是使用规则集。

缺少这些文件时，Throne 会显示“需要 Geo 资源文件”（Geo asset files required）并提供下载。点击 `是`（Yes），等待出现“Geo 资源已安装”（Geo assets installed），然后重新启动该配置档。

下载地址位于 `设置` → `基本设置` → `杂项`（Miscellaneous）→ `Xray Geo 资源`（Xray Geo Assets）中的 `GeoIP 资源 URL`（GeoIP Asset URL）和 `GeoSite 资源的 URL`（GeoSite Asset URL）字段。它们的列表提供四个来源的地址：Loyalsoldier（全球和中国，默认）、Chocolate4U（伊朗）、runetfreedom（俄罗斯）和 v2fly。两个文件请从同一来源获取。每个 `下载`（Download）按钮都会重新下载对应的文件并替换旧文件。

如果某条规则引用了文件中不存在的类别，Throne 会显示“Geo 资源缺失类别”（Geo asset missing category）。请选择包含该类别的来源，然后点击该文件对应的 `下载`。

## Android 版 {#android}

- 同样的设置位于 `设置`（Settings）→ `核心`（Core）→ `Xray VLESS 偏好`（Xray VLESS preference），选项为 `仅 XHTTP`（XHTTP only）、`XHTTP 与 Reality`（XHTTP and Reality，默认）和 `全部 VLESS`（All VLESS）。它同样只在导入时生效，因此更改后请更新订阅。
- 配置档类型同样为 `VLESS` 和 `VLESS (Xray)`，代理链中使用 Xray 的限制也相同。
- 自 2.0.4 起，Android 上的 `VLESS (Xray)` 配置档也可以使用 XDRIVE：在 `传输协议`（Network）中选择 `XDRIVE`。相关字段位于 `XDRIVE 设置`（XDRIVE settings）和 `XDRIVE 调优`（XDRIVE tuning）中，`密钥列表`（Secrets）每行填写一个值。`s3` 服务仅在桌面版中可用。
- mKCP 传输和 XDNS 仅在桌面版中可用。
- 自 2.0.4 起，`设置`（Settings）→ `核心`（Core）→ `在 Xray 中信任系统证书`（Trust system certificates in Xray）的作用与桌面版相同。它信任 Android 自带的根证书，但不信任你自己安装的证书。
- 从 2.0.2 起，当带有 `geoip:` 或 `geosite:` 规则的 Xray 配置启动或测试时，Android 版 Throne 会自动下载 `geoip.dat` 和 `geosite.dat`，通知中会显示下载进度。在 `设置`（Settings）→ `核心`（Core）→ `Xray Geo 资源`（Xray geo assets）中选择下载来源：`来源`（Source）提供与桌面版相同的四个来源，也可以选择 `自定义`（Custom）地址。在 `文件`（Files）中点按某个文件可重新下载它，点按 `立即下载`（Download now）则下载两个文件。

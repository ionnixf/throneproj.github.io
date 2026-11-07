+++
title = "OpenVPN、OpenConnect 与 Tailscale"
description = "导入并设置 OpenVPN、OpenConnect 和 Tailscale 配置档，使用一次性验证码登录，并让工作 VPN 与代理同时运行。"
weight = 50
toc = true
+++

Throne 可以连接 OpenVPN 服务器以及 Cisco AnyConnect 和兼容的 VPN（OpenConnect），还可以加入 Tailscale 网络。这些 VPN 客户端内置于核心中，因此无需另行安装 OpenVPN 或 OpenConnect。

## OpenVPN {#openvpn}

通过 `程序`（Program）→ `添加文件中的配置档`（Add profile from File(s)）（`Ctrl+O`）导入 `.ovpn` 文件，或将文件拖到主窗口上。

- 只支持 `dev tun` 的客户端配置。TAP 配置和服务器端配置会被拒绝。
- 不支持 PKCS#12 证书包。请将 CA、证书和密钥导出为 PEM 格式，再分别粘贴到 `CA 证书`（CA Certificate）、`客户端证书`（Client Certificate）和 `客户端钥匙`（Client Key）中。
- 如果该文件从单独的文件读取登录信息（`auth-user-pass`），请在配置档中填写 `用户名`（Username）和 `密码`（Password）。
- Throne 无法使用的选项会列在日志中。

## OpenConnect {#openconnect}

`类型`（Flavor）用于选择服务器类型：留空或 `anyconnect`（Cisco AnyConnect 及兼容服务器）、`gp`（Palo Alto GlobalProtect）、`fortinet`、`f5`（F5 BIG-IP）、`pulse`（Pulse Connect Secure）或 `nc`（Juniper Network Connect）。

Throne 可以从文件、剪贴板或订阅导入 AnyConnect XML 配置文件（每台服务器一个配置档）、`openconnect` 命令行（例如 `openconnect --protocol=gp --user=alice vpn.example.com`），以及包含 `protocol=` 行的配置文件。

`认证组`（Auth Group）用于预先选择组、认证域或网关。部分 GlobalProtect、F5 和 Fortinet 门户需要在 `服务器路径`（Server Path）中填写其 URL 的路径部分。

## 两种 VPN 共有的选项 {#vpn-options}

- **`仅路由通告的网络`**（Only route advertised network，默认开启）：只有发往 VPN 服务器所通告网络的流量才会进入隧道，到达该配置档的其他流量会被阻止。如果 VPN 需要承载所有流量，请关闭此项。带有 `redirect-gateway` 的 `.ovpn` 文件在导入时会关闭此项。
- **`隧道 DNS`**（Tunnel DNS）：`优先`（Prefer，默认）只通过 VPN 的 DNS 服务器解析服务器声明的域名（即其 split DNS 域名）。`严格`（Strict）还把所有远程 DNS 查询都发给它们，因此它们无法解析的域名将解析失败。`无`（None）忽略它们。

## 登录 {#sign-in}

Throne 使用已保存的 `用户名` 和 `密码` 登录。当服务器要求提供更多信息时，会打开 `VPN 认证`（VPN Authentication）窗口：

- **问题或表单**，例如第二个密码：填写后点击 `提交`（Submit）。
- **登录页面：** 点击 `在浏览器中打开`（Open in Browser），在浏览器中完成登录，然后关闭该窗口。
- **只能在浏览器内完成的单点登录（SSO）** 目前尚不支持。请使用用户名和密码；对于 OpenConnect，也可以在配置档的 `高级`（Advanced）设置中填写会话 `Cookie`。

如果服务器拒绝了已保存的登录凭据，Throne 会请你输入仅用于本次会话的凭据。`运行时统计`（Runtime Stats）标签页（仅在高级模式下显示，参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）显示每个 VPN 连接的状态。

## OTP 管理器 {#otp-manager}

许多工作 VPN 要求输入身份验证器应用生成的一次性验证码。Throne 内置了自己的身份验证器：`工具`（Tools）→ `OTP 管理器`（OTP Manager）（仅在高级模式下显示）。

- **添加条目**：`导入`（Import）→ `来自链接或文本...`（From link or text...）（`otpauth://` 链接、Google Authenticator 导出的 `otpauth-migration://`、JSON 或 Base32 密钥）、`手动添加...`（Add manually...）、`来自剪贴板`（From clipboard）或 `来自 QR 图像文件...`（From QR image file...）。`扫描二维码`（Scan QR Code）会在屏幕上查找二维码。
- **复制验证码**：点击条目，或使用托盘菜单中的 `OTP 代码`（OTP Codes）。
- `删除`（Delete）会永久移除条目。

OTP 条目会包含在[备份](@/guides/backup.zh.md#backup-restore)中（`OTP 配置档`（OTP profiles）），请妥善保管备份文件。

### 在 VPN 配置档中使用验证码 {#bind-otp}

1. 打开 OpenVPN 或 OpenConnect 配置档，在 `OTP` 中选择对应条目。
2. 如果服务器要求把验证码作为密码的一部分，请在验证码所在的位置写上 `{otp}`，例如 `MyPassword{otp}`。对于 OpenConnect，`{otp}` 在软件令牌和表单字段中同样有效。
3. 点击 `确定`（OK）。

此后 Throne 每次连接时都会填入新的验证码，并自动回应服务器的验证码提示。对于 OpenVPN 静态质询，请在 `静态质询`（Static Challenge）中填写质询文本。

## 分离隧道：让工作 VPN 与代理并行 {#split-tunnel}

路由配置档可以在你运行的配置档之外，同时启动 OpenVPN 或 OpenConnect 配置档。发往 VPN 所通告网络的流量经由 VPN 传输，其余流量按路由配置档处理。

1. 打开 `路由`（Routing）→ `路由设置`（Routing Settings）→ `路由`（Route），选中你正在使用的路由配置档，点击 `编辑`（Edit）。
2. 在 `端点`（Endpoints）标签页（仅高级模式）中选择该 VPN 配置档，点击 `添加`（Add），然后点击 `确定`。拥有 OpenVPN 或 OpenConnect 配置档后，该标签页才会出现。
3. 确认该路由配置档处于启用状态，然后启动你平时使用的配置档，VPN 会随之启动。

每个端点都会在 `高级`（Advanced）标签页中添加一条 `… route prefer` 规则。它只能移动：位于它上方的规则会先被检查。

- 用作端点的配置档不能同时是你启动的配置档，也不能是其中的跳点。
- 端点可以是出口为 OpenVPN 或 OpenConnect 配置档的代理链。`允许路由到内部跳点`（Allow routing to inner hops）还会路由到代理链内部的此类跳点。
- 端点中的跳点不能运行在 Xray 上，也不能是额外核心或完整配置类配置档。

完整示例请参见[常用方案](@/guides/recipes.zh.md#corporate-vpn)。

## Tailscale {#tailscale}

Tailscale 配置档会加入你的 tailnet，让你可以访问自己的 Tailscale 设备，或将其中一台用作出口节点。通过 `程序` → `新建配置档`（New profile）→ `类型`（Type）`Tailscale` 创建：

| 字段 | 作用 |
| --- | --- |
| `认证钥匙`（Auth key） | 用于登录你的 tailnet 的密钥，在 Tailscale 管理控制台中创建。 |
| `控制 URL`（Control URL） | 仅在使用自建控制服务器时才需更改。 |
| `主机名`（Hostname） | 此设备在 tailnet 中的名称。 |
| `出口节点`（Exit node） | 用于发送互联网流量的 tailnet 设备。`出口节点允许LAN访问`（Exit node allow lan access）可保留对本地网络的访问。 |
| `接受路由`（Accept routes） | 使用其他设备通告的子网路由。 |
| `通告出口节点`（Advertise exit node）、`通告路由`（Advertise routes） | 将这台电脑作为出口节点提供出去，或提供子网（以逗号分隔）。 |
| `Ephemeral (短暂)` | 该设备离线后会被 tailnet 移除。 |
| `状态目录`（State directory）、`全局 DNS`（Global DNS） | 保存登录状态的位置（`$HOME/.tailscale`），以及 Tailscale 的 DNS 是否也使用 tailnet 的全局域名服务器。 |

tailnet 名称（位于 `ts.net` 下）使用 Tailscale 的 DNS 解析，其他域名使用你平常的 DNS 设置。URL 测试和自动选择器会跳过 Tailscale 配置档。

## Android 版 {#android}

- OpenVPN 和 OpenConnect 配置档同样可用，支持相同的导入格式，但没有 OTP、`仅路由通告的网络` 和 `隧道 DNS`。配置档必须使用已保存的用户名和密码登录。桌面版备份中的 OTP 条目不会被恢复。
- 路由配置档端点（分离隧道）无法运行。来自桌面版备份的路由配置档会保留端点，但 Android 不会启动它们。
- 不支持 Tailscale 配置档。在两个平台上，自动选择器都会跳过 OpenVPN 和 OpenConnect 配置档。

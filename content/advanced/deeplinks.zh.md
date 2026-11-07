+++
title = "深度链接"
description = "在桌面版和 Android 上使用 throne:// 链接，一键添加订阅、路由配置档和代理配置档。"
weight = 70
toc = true
+++

深度链接是一种 `throne://` URL，用来让 Throne 添加订阅、路由配置档、远程路由配置档或一个代理配置档。服务商会把深度链接放在网站上或二维码中。

## Throne 如何接收深度链接 {#receive}

只有当 `throne://` 已注册给 Throne 时，点击链接才有效。Windows 安装程序、Linux 的 `.deb` 和 `.rpm` 软件包及安装脚本，以及 macOS 上的 `Throne.app` 都会为你的用户账户注册它。Windows 或 Linux 上从 ZIP 解压的副本不会注册：打开 `设置`（Settings）→ `基本设置`（Basic Settings）→ `通用`（Common），勾选 `启动时注册 throne:// 链接`（Register throne:// links at startup）（仅在高级模式下显示，参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)），然后点击 `确定`（OK）。如果你保留了多个副本，链接会在最后一个开启此选项并启动的副本中打开；选项旁边的 `安装`（Install）会注册当前副本。

未注册时，复制链接并在主窗口中按 `Ctrl+V`，或将链接文本拖放到主窗口上。Throne 也接受作为[命令行参数](@/reference/files.zh.md#command-line)传入的链接。

{% alert_info() %}
在桌面版上，`扫描二维码`（Scan QR Code）和拖放的二维码图片只会导入 `add` 链接。如果二维码中是其他深度链接，请从 `日志`（Logs）标签页复制 `QR Code Result:` 之后的文本，然后按 `Ctrl+V`。
{% end %}

[无界面 Throne](@/headless/_index.zh.md) 不处理 `throne://` 链接。请用 `thronectl sub add` 添加订阅，用 `thronectl import` 导入 `add` 链接，参见 [Throne API 与 thronectl](@/advanced/api.zh.md#commands)。

## 链接格式 {#format}

```text
throne://<command>/<payload>
```

- `throne://` 必须小写。命令不区分大小写。
- 命令后面的斜杠是必需的。
- Throne 会忽略链接中 `?` 或 `#` 之后的所有内容，因此全部信息都要放进载荷（payload）。
- 经过百分号编码的载荷（例如用 `%2B` 代替 `+`）会先被解码。

| 命令 | 作用 | 是否先询问？ |
| --- | --- | --- |
| `addsub` | 添加一个订阅分组并更新它 | 是 |
| `route` | 导入链接中携带的路由配置档 | 是 |
| `remoteroute` | 按 URL 添加远程路由配置档 | 是 |
| `add` | 向当前分组添加一个代理配置档 | 否。在 Android 上，在其他应用中点按该链接时会询问。 |

### Base64 规则 {#base64}

| 命令 | 接受的载荷 |
| --- | --- |
| `addsub`、`remoteroute` | 仅限标准 Base64（`+` 和 `/`），带不带 `=` 填充均可。包含 `-` 或 `_` 的 URL 安全 Base64 会失败。 |
| `route`、`add` | URL 安全的 Base64（`-` 和 `_`）或标准 Base64，带不带填充均可。Throne 自己生成的是不带填充的 URL 安全 Base64。 |

请让载荷保持在一行内，不要包含空格。在 Linux 或 Git Bash 中编码（在 macOS 上去掉 `-w 0`）：

```bash
printf '%s' 'https://example.com/sub/abc123#MyProvider' | base64 -w 0
```

在 PowerShell 中：

```powershell
[Convert]::ToBase64String([Text.Encoding]::UTF8.GetBytes('https://example.com/sub/abc123#MyProvider'))
```

这两条命令输出的都是带填充的标准 Base64。

## `addsub`：添加订阅 {#addsub}

```text
throne://addsub/<base64>
```

载荷是 `<subscription_url>#<group_name>` 的 Base64。URL 保持原样，无需进行百分号编码。`#<group_name>` 部分可选（默认为 URL 的主机名），其中直接使用空格也可以。例如，`https://example.com/sub/abc123#MyProvider` 得到：

```text
throne://addsub/aHR0cHM6Ly9leGFtcGxlLmNvbS9zdWIvYWJjMTIzI015UHJvdmlkZXI=
```

Throne 会先询问，并显示一个默认已勾选的 `自动更新`（Auto update）复选框，然后创建该分组并更新它。

{% alert_info() %}
`自动更新` 只决定该分组是否参与自动更新。自动更新还需要开启 `基本设置` → `订阅`（Subscription）→ `订阅自动更新`（Subscription auto update）→ `启用`（Enable），该选项默认关闭。参见[订阅与分组](@/guides/subscriptions.zh.md#auto-update)。
{% end %}

## `route`：导入路由配置档 {#route}

```text
throne://route/<base64>
```

携带一个完整的路由配置档。要生成这种链接，请在 `路由设置`（Routing Settings）的 `路由`（Route）标签页中选中该配置档并点击 `导出`（Export）（`Ctrl+C`）。导入的配置档在你从 `路由`（Routing）菜单中选择它之前不会启用。

载荷是一个 JSON 对象的 Base64：

```json
{
  "kind": "throne-route-profile",
  "v": 1,
  "name": "Example",
  "default_outbound": "proxy",
  "rules": []
}
```

```text
throne://route/eyJraW5kIjoidGhyb25lLXJvdXRlLXByb2ZpbGUiLCJ2IjoxLCJuYW1lIjoiRXhhbXBsZSIsImRlZmF1bHRfb3V0Ym91bmQiOiJwcm94eSIsInJ1bGVzIjpbXX0
```

- `default_outbound` 的值为 `proxy`、`direct`、`block` 或 `warp-bypass`。
- 原始配置档带有 `"raw": true` 和一个 `route` 对象，而不是 `rules`。
- 可选的 `dns` 对象携带配置档自己的 DNS 设置：`remote_dns`、`remote_dns_disable_ipv6`、`direct_dns`、`direct_dns_disable_ipv6`、`dns_final_out`、`enable_dns_routing`、`fake_dns` 和 `fakeip_disable_ipv6`（参见[按路由配置档设置 DNS](@/guides/dns.zh.md#route-dns)）。规则可以带有 `"disabled": true`。
- 规则按名称引用要发送流量的已保存服务器。Throne 会告诉你哪些名称没有找到。

`路由` 标签页上的 `导入`（Import）（`Ctrl+V`）也接受单独的 Base64 载荷或纯 JSON。参见[分享配置档](@/guides/routing.zh.md#share-profiles)。

## `remoteroute`：添加远程路由配置档 {#remoteroute}

```text
throne://remoteroute/<base64>
```

只携带 URL：Throne 会下载每个路由配置档，并可以使其保持最新。可用它来分发你自己维护的路由配置档。载荷是一份纯文本列表（每行一个 URL）的 Base64，而不是 JSON：

```text
<profile_url_1>#<name_1>
<profile_url_2>#<name_2>
```

- 不以 `http://` 或 `https://` 开头的行会被跳过。`#<name>` 可选（默认为 URL 的主机名）。
- 每个 URL 都必须提供一个结构化路由配置档：一条 `throne://route/` 链接、其 Base64 载荷或其 JSON。原始配置档不能用作远程配置档。

例如，这条命令：

```bash
printf '%s\n%s' 'https://example.com/routes/bypass-iran.json#BypassIran' 'https://example.com/routes/ads.json' | base64 -w 0
```

得到这条链接：

```text
throne://remoteroute/aHR0cHM6Ly9leGFtcGxlLmNvbS9yb3V0ZXMvYnlwYXNzLWlyYW4uanNvbiNCeXBhc3NJcmFuCmh0dHBzOi8vZXhhbXBsZS5jb20vcm91dGVzL2Fkcy5qc29u
```

Throne 会先询问，并为所有配置档显示一个共用的、默认已勾选的 `自动更新` 复选框，然后下载它们。

- 更新会替换配置档的规则和默认出站，但不会更改其名称。
- 定时更新还需要开启 `基本设置` → `订阅` → `路由配置档自动更新`（Routing profiles auto update）→ `启用`（仅在高级模式下显示），该选项默认关闭。参见[远程配置档](@/guides/routing.zh.md#remote-profiles)。
- 位于 `raw.githubusercontent.com` 上的 URL 会通过 `远程规则集镜像`（Remote Rule-set Mirror）下载。在你修改文件后的一段时间内，镜像可能仍提供旧版本。

## `add`：添加一个代理配置档 {#add}

```text
throne://add/<base64>
```

载荷是该配置档出站设置（JSON 格式）的 Base64。在桌面版上，配置档会被立即添加到当前分组，就像粘贴了一条 `vless://` 链接一样。凡是分享链接可用的地方，`add` 链接也同样可用：订阅中、文件中以及二维码中。

要生成这种链接，请右键点击配置档 → `分享`（Share）→ `复制选定项的链接 (深度链接)`（Copy links of selected (Deep Links)）（`Ctrl+Alt+C`，仅在高级模式下显示），或使用 `分享` → `二维码和链接`（QR Code and link）并勾选 `Deep Link`。深度链接携带完整的配置，包括绑定网络接口等高级连接选项。`复制选定项的链接`（Copy links of selected，`Ctrl+C`）只会为没有标准分享链接的配置档生成深度链接。

## Android 版 {#android}

Throne for Android 支持同样的四个命令，并可以生成 `add` 和 `route` 链接。它还接受 `clash://install-config?url=…&name=…` 链接，这类链接会像 `addsub` 一样添加订阅；`url` 的值需要进行百分号编码。`throne://` 协议在安装应用时即完成注册。

在其他应用中点按链接，或在 `配置`（Profiles）界面使用 `添加服务器配置`（Add profile）→ `从剪切板导入`（Import from clipboard）或 `扫描二维码`（Scan QR code）。二维码适用于全部四个命令，`route` 和 `remoteroute` 链接也可以通过 `路由`（Routing）界面上的 `导入`（Import）导入。与桌面版的区别：

- 在其他应用中点按 `add` 链接时会先询问。通过剪贴板或二维码导入时则不会询问。
- 原始路由配置档（`"raw": true`）会被拒绝，分享的路由配置档中的端点会被丢弃。
- 定时更新需要开启 `设置`（Settings）→ `订阅`（Subscriptions）→ `订阅自动更新`（Subscription auto update），远程路由配置档还需要开启 `设置` → `路由` → `自动更新远程配置`（Auto update remote profiles）。两者默认均为关闭。

## 故障排除 {#troubleshooting}

以下消息来自 Throne 桌面版。

- **点击链接没有反应，或浏览器询问要使用哪个应用。** 当前副本没有注册 `throne://`，参见 [Throne 如何接收深度链接](#receive)。如果你保留了多个副本，请在应当打开链接的那个副本中点击 `安装`。也可以复制链接，然后在 Throne 中按 `Ctrl+V`。
- **Linux：链接仍然无法打开。** 安装 `desktop-file-utils`，然后点击 `启动时注册 throne:// 链接` 旁边的 `安装`。
- **没有任何反应，日志也是空的。** Throne 会不加提示地丢弃这些链接：载荷不是有效标准 Base64 的 `addsub` 链接（URL 安全的 `-` 或 `_`、空格或换行），命令后面缺少斜杠的链接（`add` 和 `remoteroute` 除外），以及命令未知且载荷无法解码的链接。
- **“忽略了带未知命令的深度链接: …”（Ignored deeplink with unknown command: …）** `throne://` 后面的单词不是 `addsub`、`route`、`remoteroute` 或 `add`。
- **“该链接不包含订阅 URL。”（The link did not contain a subscription URL.）** `addsub` 载荷中 `#` 前面没有 URL。
- **“Deep link has no data”（深度链接没有数据）或“Base64 is invalid.”（Base64 无效）。** `throne://remoteroute/` 后面没有内容，或者载荷不是标准 Base64。
- **“The link did not contain any valid http(s) routing profile URLs.”（该链接不包含任何有效的 http(s) 路由配置档 URL）** `remoteroute` 载荷中没有任何一行以 `http://` 或 `https://` 开头。请发送 URL 列表，而不是 JSON。
- **“该链接不包含任何有效的远程路由配置档。”（The link did not contain any valid remote routing profiles.）** 链接中 `remoteroute` 后面缺少斜杠。
- **“该链接无法解析:”（The link could not be parsed:）**（`route` 链接）。“Empty input”（输入为空）：`throne://route/` 后面没有内容。“Input is not valid JSON, base64, or a Throne route link”（输入不是有效的 JSON、base64 或 Throne 路由链接）：载荷已损坏或被截断。“Unrecognized route object”（无法识别的路由对象）：JSON 中没有 `"kind": "throne-route-profile"`。
- **日志中出现“导入了 0 个配置档”（Imported 0 profile(s)）。** `add` 载荷已损坏，或者你扫描的二维码中是其他类型的深度链接。
- **日志中出现“远程路由配置档 … 失败: …”（Remote routing profile … failed: …）。** 配置档已添加，但其 URL 无法下载，或者提供的不是结构化路由配置档。修正后，点击 `路由` 标签页上的 `更新`（Update）。

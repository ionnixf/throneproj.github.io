+++
title = "路由"
description = "借助现成的路由配置档或你自己编写的规则，决定哪些流量经过代理、哪些直连、哪些被阻止。"
weight = 40
toc = true
+++

路由决定每个连接的去向：经过代理、直连，或者哪里也不去。内置的 `Default` 配置档会让所有流量经过代理，所以只有在你需要其他效果时才需要本页。

## 路由的工作原理 {#how-routing-works}

路由配置档由一组规则和一个默认出站组成。Throne 从上到下检查这些规则，第一条匹配的规则决定连接的去向。如果没有任何规则匹配，则使用默认出站。

| 出站 | 流量去向 |
| --- | --- |
| `proxy` | 经过正在运行的配置档；开启 WARP 时还会经过 WARP。 |
| `direct` | 直接连接互联网。 |
| `block` | 哪里也不去。连接会被拒绝。 |
| `warp-bypass` | 经过正在运行的配置档，但不经过 WARP。参见 [WARP](@/advanced/warp.zh.md#warp-bypass)。 |
| 已保存的配置档 | 改为经过该配置档。仅可在高级规则中使用。 |

同一时间只有一个路由配置档处于活动状态。可以在 `路由`（Routing）菜单底部的列表中切换，或通过托盘菜单 → `选择路由`（Select Routing）切换。正在运行的连接会以新规则重启。分组也可以使用自己的路由配置档，参见[按分组设置路由配置档](#group-routing)。

## 下载现成的配置档 {#download-profiles}

1. 打开 `路由`（Routing）→ `下载配置档`（Download Profiles），然后选择 `China`、`Iran` 或 `Russia`。
2. 保持勾选 `自动更新`（Auto update），然后点击 `确定`（OK）。
3. 在 `路由` 菜单底部选择其中一个新配置档。仅下载并不会启用它。

| 配置档 | 作用 |
| --- | --- |
| `Bypass China`、`Bypass Iran`、`Bypass Russia` | 除该国的网站和 IP 地址以及你的本地网络外，其余流量全部走代理。 |
| `Proxy China Blocked`、`Proxy Russia Blocked` | 所有流量都直连，但在该国被封锁的网站除外。 |
| `Proxy Antizapret`、`Proxy Refilter`（俄罗斯） | 所有流量都直连，但 Antizapret 或 Re:filter 封锁列表中的网站除外。 |

如果你访问的大多数网站都在境外，请使用“Bypass”类配置档。“Proxy … Blocked”类配置档经过你服务器的流量更少，但被封锁却不在列表中的网站将无法打开。

这些列表维护在 [throneproj/routeprofiles](https://github.com/throneproj/routeprofiles) 中。如果下载失败，请另选一个 `远程规则集镜像`（Remote Rule-set Mirror，参见[规则集](#rule-sets)），然后重试。

## 路由配置档 {#profiles}

路由配置档在 `设置`（Settings）→ `路由设置`（Routing Settings）的 `路由`（Route）标签页上管理，`路由`（Routing）菜单的第一项也能打开它。在 `路由` 菜单中右键点击某个配置档，可以直接在编辑器中打开它。更改在你点击 `确定`（OK）后生效；如果有连接正在运行，请点击“设置已更改，重启进行应用”提示中的 `重启`（Restart）。

配置档分为三种：

- **结构化配置档**，在 Throne 中通过[简单规则](#simple-rules)和[高级规则](#advanced-rules)编辑。大多数人只需要这一种。
- **[远程配置档](#remote-profiles)**，从 URL 下载。
- **[原始配置档](#raw-profiles)**，以 sing-box JSON 编写。

创建结构化配置档的步骤：

1. 在 `路由`（Route）标签页上，点击 `新建`（New）→ `结构化配置档`（Structured profile）。
2. 输入 `名称`（Name），并选择 `默认出站`（Default outbound）。
3. 在 `基本`（Basic）或 `高级`（Advanced）标签页上添加规则，然后点击 `确定`（OK）。
4. 在 `路由`（Routing）菜单中启用它。

配置档的 `DNS` 标签页可以为它设置自己的 DNS 服务器，参见[按路由配置档设置 DNS](@/guides/dns.zh.md#route-dns)。当你有 OpenVPN 或 OpenConnect 配置档时会出现 `端点`（Endpoints）标签页，它会让这些配置档与代理同时运行，参见[分离隧道](@/advanced/vpn_profiles.zh.md#split-tunnel)。

`通用`（Common）标签页上还有以下设置（仅高级模式，参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）：

| 设置 | 作用 |
| --- | --- |
| `默认域策略`（Default Domain Strategy） | Throne 解析服务器或直连网站的地址时，对 IPv4 或 IPv6 的偏好。 |
| `解析域策略`（Resolve Domain Strategy） | 在规则运行之前解析每个域名，这样 IP 规则也能匹配域名。 |
| `远程规则集镜像`（Remote Rule-set Mirror） | 存放在 GitHub 上的规则集和配置档从何处下载。默认值：`jsDelivr(Cloudflare)`。 |

## 简单规则 {#simple-rules}

配置档的 `基本`（Basic）标签页中有四个输入框：`直连`（Direct）、`代理`（Proxy）、`阻止`（Block）和 `Warp-bypass`。输入框中的每一行都会把匹配的流量发送到对应的去向。

| 行 | 匹配 |
| --- | --- |
| `domain:example.com` | 仅 `example.com`。 |
| `suffix:example.com` | `example.com` 及其所有子域名。 |
| `keyword:example` | 所有包含 `example` 的域名。 |
| `regex:^cdn[0-9]*\.example\.com$` | 匹配某个正则表达式（Go RE2 语法）的域名。 |
| `ruleset:geosite-youtube` | [规则集](#rule-sets)中的所有内容，按名称或 `.srs` URL 指定。 |
| `ip:10.0.0.0/8` | 一个 IP 范围，或单个地址，例如 `1.2.3.4`。 |
| `processName:Telegram.exe` | 某个程序，按文件名匹配。 |
| `processPath:C:\Program Files\App\app.exe` | 某个程序，按完整路径匹配。 |

- 每行写一个条目。前缀区分大小写：写 `processName`，而不是 `processname`。
- 进程名必须完全匹配，包括大小写，在 Windows 上还包括 `.exe`。[连接标签页](#connections-tab)会显示确切的名称。
- 编辑器会标出无法使用的行。请在切换标签页之前修正它们，因为切换标签页会丢弃这些行。

{% alert_info() %}
进程规则只能看到到达 Throne 的流量。在系统代理模式下，忽略代理的程序根本不会到达 Throne，因此请将进程规则与 `Tun 模式`（Tun Mode）配合使用。参见 [TUN 模式](@/guides/tun_mode.zh.md#enable)。
{% end %}

**示例：除本国网站外全部走代理，并屏蔽广告。** 将 `默认出站`（Default outbound）设为 `proxy`。在 `直连`（Direct）中填写：

```text
ruleset:geosite-ir
ruleset:geoip-ir
```

在 `阻止`（Block）中填写：

```text
ruleset:geosite-category-ads-all
```

将 `ir` 替换为你所在国家的代码，例如 `cn` 或 `ru`。

**示例：只让一个程序和一个网站走代理。** 将 `默认出站`（Default outbound）设为 `direct`。在 `代理`（Proxy）中填写：

```text
processName:Telegram.exe
suffix:example.org
```

每个输入框都会变成 `高级`（Advanced）标签页上的规则，例如 `直连` 中的地址会变成 `Simple Address Bypass`，`代理` 中的程序会变成 `Simple Process Name Proxy`。它们按 `直连`、`阻止`、`代理`、`Warp-bypass` 的顺序添加，而第一条匹配的规则胜出，因此如果 `直连` 中范围更广的某一行匹配了同样的流量，`代理` 中的行就不会生效。要设置例外，例如在 `geosite-ir` 走直连时让 `example.ir` 走代理，请将 `suffix:example.ir` 添加到 `代理`，然后在 `高级` 标签页上把 `Simple Address Proxy` 移到包含 `geosite-ir` 的规则之上。

## 高级规则 {#advanced-rules}

`高级`（Advanced）标签页按顺序列出配置档的所有规则。`上移`（Move Up）和 `下移`（Move Down）用于调整顺序。取消勾选某条规则的复选框即可禁用它而不删除；这同样适用于 `基本`（Basic）输入框生成的规则。在简洁模式下，只有当配置档中有 `基本` 标签页无法显示的规则时，才会显示该标签页。

要添加规则，请点击 `新建`（New）并选择一个 `动作`（Action）。然后在 `+` 标签页上勾选所需的属性并填写，每行一个条目。

| 动作 | 作用 |
| --- | --- |
| `route` | 将匹配的流量发送到规则的 `outbound`。新规则使用 `direct`，直到你勾选 `outbound` 并选择其他出站。 |
| `reject` | 阻止匹配的流量。在高级规则中，请用它代替 `block`。 |
| `hijack-dns` | 用 Throne 的 DNS 应答 DNS 查询。 |
| `route-options` | 设置 `override_address` 或 `tls_spoof` 等选项，然后继续执行后续规则。 |
| `sniff` | 探测连接的协议和域名，然后继续。 |
| `resolve` | 查询域名的 IP 地址，然后继续。 |
| `bypass` | 仅限 Linux。参见 [sing-box 文档](https://sing-box.sagernet.org/configuration/route/rule_action/)。 |

`route`、`route-options` 和 `bypass` 还提供 `tls_fragment` 和 `tls_record_fragment`。它们会像配置档的 [TLS 分片](@/advanced/presets.zh.md#tls-fragment)选项一样拆分 TLS 握手，有助于 `direct` 连接访问被过滤器按名称拦截的网站。

| 属性 | 匹配内容 |
| --- | --- |
| `domain`、`domain_suffix`、`domain_keyword`、`domain_regex` | 请求的域名。 |
| `ip_cidr`、`ip_is_private` | 目标地址。 |
| `rule_set` | 规则集名称或 `.srs` URL。 |
| `port`、`port_range` | 目标端口，例如 `443` 或 `1000:2000`。 |
| `source_ip_cidr`、`source_port` 等 | 连接的来源，例如你网络中的另一台设备。 |
| `process_name`、`process_path`、`process_path_regex` | 本机上的程序。 |
| `network`、`protocol`、`ip_version` | `tcp`、`udp` 或 `icmp`；探测到的协议，例如 `tls` 或 `bittorrent`；`4` 或 `6`。 |
| `inbound` | `mixed-in`（代理端口）或 `tun-in`（TUN 模式）。 |
| `wifi_ssid`、`wifi_bssid` | 你所连接的 Wi-Fi 网络。在 macOS 上不可用。 |
| `invert` | 让规则在其条件不匹配时匹配。 |

在同一条规则中，域名、IP 和 `rule_set` 属性只要有任意一个匹配即可。其他所有属性也必须同时匹配。

要让流量经过指定的服务器，请勾选 `outbound` 并选择一个已保存的配置档，它显示为 `[group] name`。Throne 会在你启动的配置档之外同时运行它。额外核心（Extra Core）配置档和完整的自定义配置不能这样使用。

## 规则集 {#rule-sets}

规则集是存放在单独文件中的域名或 IP 范围列表。可以在输入框中以 `ruleset:<name>` 的形式使用，或在高级规则的 `rule_set` 属性中使用。

- **内置名称**：2000 多个。以 `geosite-` 开头的名称包含域名，例如 `geosite-youtube` 或 `geosite-category-ads-all`。以 `geoip-` 开头的名称包含 IP 范围，例如 `geoip-ir` 或 `geoip-telegram`。编辑器会在你输入时提示名称。
- **你自己的文件**：任何 sing-box 二进制规则集的 URL。文件名必须包含 `.srs`。

规则集会在配置档启动时下载。`路由`（Routing）→ `更新规则集`（Update Rule-Sets）会下载最新副本。如果 GitHub 在你的网络中被封锁或速度很慢，请更改 `路由设置`（Routing Settings）→ `通用`（Common）→ `远程规则集镜像`（Remote Rule-set Mirror）。该镜像同样适用于 AdBlock 列表和存放在 GitHub 上的路由配置档。

## 远程配置档 {#remote-profiles}

远程配置档是 Throne 从 URL 下载的结构化配置档，例如通过[下载配置档](#download-profiles)获得的那些。服务商也可以通过 [`throne://remoteroute/` 链接](@/advanced/deeplinks.zh.md#remoteroute)分享远程配置档。

要添加远程配置档，请在 `路由`（Route）标签页上点击 `新建`（New）→ `远程配置档`（Remote profile），输入 `URL`，勾选 `自动更新`（Auto update），然后点击 `确定`（OK）。该 URL 可以提供 `throne://route/` 链接、链接的 Base64 文本或配置档的 JSON。原始配置档不能作为远程配置档。

自动更新还需要开启 `设置`（Settings）→ `基本设置`（Basic Settings）→ `订阅`（Subscription）→ `路由配置档自动更新`（Routing profiles auto update），该项默认关闭（仅高级模式）。要立即更新，请点击 `路由`（Route）标签页上的 `更新`（Update）。

{% alert_warning() %}
更新会替换配置档的规则和默认出站，因此你对它所做的更改会丢失。要自定义远程配置档，请先 `克隆`（Clone）它，然后在副本中取消勾选 `自动更新`（Auto update）。你禁用的规则和配置档自己的 DNS 设置会保留。
{% end %}

## 按分组设置路由配置档 {#group-routing}

分组可以使用自己的路由配置档连接，而不是使用 `路由`（Routing）菜单中的那个。例如，某个服务商的服务器可以使用 `Bypass Iran`，而其他所有分组都使用 `Default`。

1. 右键点击该分组的标签页并选择 `编辑选定分组`（Edit selected Group）。
2. 选择一个 `路由配置档`（Routing Profile）。`默认（…）`（Default (…)）跟随 `路由` 菜单。
3. 点击 `确定`（OK）。

由你启动的配置档所在的分组决定。对于代理链或自动选择器，看的是代理链或自动选择器本身所在的分组，而不是其中各个配置档所在的分组。窗口标题和托盘提示会显示正在使用的路由配置档，[连接标签页](#connections-tab)修改的也是这个配置档。分组的路由配置档也会带上它自己的 [DNS 设置](@/guides/dns.zh.md#route-dns)（如果有的话）。

### 来自订阅 {#subscription-routing}

有些服务商会随订阅一起发送 Happ 格式的路由配置档。要使用它，请打开分组设置，在 `订阅`（Subscription）下勾选 `使用订阅提供的路由配置档`（Use the subscription's routing profile），点击 `确定`（OK），然后更新该订阅。

Throne 会把它保存为一个结构化路由配置档，使用服务商给出的名称。如果该分组的 `路由配置档`（Routing Profile）是 `默认（…）`（Default (…)），新配置档会成为该分组的路由配置档。每次更新都会替换它的规则。订阅中的 DNS 设置从不使用。

- 要永久修改它，请 `克隆`（Clone）它，并把副本选为该分组的 `路由配置档`（Routing Profile）。
- 该配置档属于这个分组。删除分组会同时删除它。

## 原始配置档 {#raw-profiles}

原始配置档是一个完整的 JSON 格式的 sing-box `route` 对象，用于规则编辑器未提供的功能。可通过 `新建`（New）→ `原始配置档`（Raw profile）创建。

出站以数字表示：`-1` 是 `proxy`，`-2` 是 `direct`，`-5` 是 `warp-bypass`，配置档 ID 则表示该已保存的配置档。在 `"outbound":` 之后，编辑器会提示你的配置档。如果缺少 `final`，Throne 会使用 `proxy`。

原始配置档中没有定义内置规则集名称，Throne 也不会添加结构化配置档会获得的 DNS 步骤。对于 TUN 模式，请像这样开始编写规则：

```json
{
  "rules": [
    { "action": "sniff" },
    { "protocol": "dns", "action": "hijack-dns" },
    { "domain_suffix": ["example.com"], "outbound": -2 }
  ],
  "final": -1
}
```

`防止修改`（Prevent modifications）会完全按原样使用该 JSON，这可能导致 DNS、Xray 配置档和代理链失效。AdBlock、`启用 DNS 路由`（Enable DNS Routing）以及连接标签页的操作都不适用于原始配置档，Throne for Android 也无法使用原始配置档。

## 连接标签页 {#connections-tab}

主窗口底部的 `连接`（Connections）标签页按程序分组列出当前打开的连接，并显示它们的目标、协议、出站和流量。将鼠标悬停在 `出站`（Outbound）列上可以查看连接匹配的规则；`final` 表示没有规则匹配。

右键点击某个连接，可以更改此类流量的去向：

- `直连`（Direct）、`代理`（Proxy）或 `阻止`（Block）会列出带复选框的目标：每一级域名（例如 `*.example.com`）、作为关键字的网站名称、连接没有域名时的 IP 地址，以及程序。勾选的目标会被添加到正在使用的路由配置档的对应输入框中，并从其他输入框中移除。
- `Specific server` 的作用相同，但会让目标经由你选择的服务器。

然后点击“设置已更改，重启进行应用”提示中的 `重启`（Restart）。目标会加入该输入框已有的规则。如果该输入框原本为空，它的新规则会添加到列表末尾，位于其上方、范围更广的规则仍可能先匹配，因此请在 `高级`（Advanced）标签页上检查顺序。

对于原始配置档、启用了 `防止修改`（Prevent modifications）的配置档、开启了 `自动更新`（Auto update）的远程配置档，以及由订阅更新的配置档，这些操作不可用。要修改远程配置档或来自订阅的配置档，请先克隆它，参见[远程配置档](#remote-profiles)和[来自订阅](#subscription-routing)。

如果没有该标签页，请开启 `设置`（Settings）→ `基本设置`（Basic Settings）→ `样式`（Style）→ `连接统计`（Connection statistics）（仅高级模式）。

## AdBlock（广告屏蔽） {#adblock}

`路由`（Routing）→ `启用 AdBlock (广告屏蔽)`（Enable AdBlock）会使用 217heidai/adblockfilters 项目的 `adblocksingbox` 列表，在当前活动的结构化或远程配置档中屏蔽广告。它在你自己的规则之前运行，因此无论是直连流量还是代理流量，广告都会被屏蔽。

如果某个网站无法正常使用，请关闭 AdBlock，检查是否是该列表屏蔽了它。如果只想在某一个配置档中屏蔽广告，请改为在该配置档的 `阻止`（Block）输入框中填写 `ruleset:geosite-category-ads-all`。

## 分享配置档 {#share-profiles}

在 `路由`（Route）标签页上选中一个配置档，然后点击 `导出`（Export）（`Ctrl+C`），即可将其复制为 `throne://route/` 链接。

要导入配置档，请点击链接，或者复制链接后在主窗口中按 `Ctrl+V`。这会添加该配置档，但不会将其设为活动配置档。在 `路由`（Route）标签页上使用 `导入`（Import）（`Ctrl+V`）还会选中它，因此点击 `确定`（OK）后它就会成为活动配置档。

链接包含名称、默认出站、规则（包括已禁用的规则）以及配置档自己的 DNS 设置。将流量发送到已保存配置档的规则会按名称引用该配置档；如果另一台设备上没有同名配置档，这些规则会使用 `proxy`。OpenVPN 和 OpenConnect 端点在分享时不包含其凭据。关于链接格式，请参见[深度链接](@/advanced/deeplinks.zh.md#route)。

## Android 版 {#android}

Throne for Android 拥有相同的路由配置档、规则和规则集。从侧边菜单打开 `路由`（Routing），点按某个配置档即可将其设为活动配置档。不同之处如下：

- **应用规则。** `应用`（Apps）字段匹配 Android 应用；`未知应用`（Unknown apps）匹配找不到所属应用的连接。`包名正则`（Package name regex）匹配包名。这些规则在桌面版上永远不会匹配。
- **网络规则。** `网络类型`（Network type）匹配 Wi-Fi、移动数据或以太网，`按流量计费网络`（Metered network）匹配按流量计费的网络。桌面版在导入链接时会排除这些规则。
- **Wi-Fi 规则**需要将位置权限设为“始终允许”（Allow all the time），并开启位置信息。
- 不支持**原始配置档**，也不支持导入的链接中的 VPN 端点。
- `AdBlock` 和 `自动更新远程配置`（Auto update remote profiles）位于 `设置`（Settings）→ `路由`（Routing）中。
- 如果某个规则集无法下载，`仍然连接`（Connect anyway）会在没有它的情况下连接。在通过代理下载到它之前，使用它的规则不会匹配任何内容。

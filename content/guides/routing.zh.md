+++
title = "路由"
description = "借助现成的路由配置档或你自己编写的规则，决定哪些流量经过代理、哪些直连、哪些被阻止。"
weight = 40
toc = true
+++

路由决定每个连接的去向：经过你的代理、直接连接互联网，或者哪里也不去。本页介绍如何为你所在的国家下载现成的路由配置档、编写自己的规则以及分享配置档。如果你希望所有流量都经过代理，则无需做任何更改。

## 路由的工作原理 {#how-routing-works}

路由配置档由一组规则和一个默认出站组成。每当一个连接开始时，Throne 会从上到下检查这些规则。第一条匹配的规则决定该连接的去向。如果没有任何规则匹配，连接将使用默认出站。

出站是流量可以去往的地方：

| 出站 | 流量去向 |
| --- | --- |
| `proxy` | 经过你启动的配置档。开启 WARP 时，WARP 会作为最后一跳加入。 |
| `direct` | 不经过代理，直接连接互联网。 |
| `block` | 哪里也不去。连接会被拒绝。 |
| `warp-bypass` | 经过你启动的配置档，但不经过 WARP。WARP 关闭时，与 `proxy` 相同。参见 [WARP](@/advanced/warp.zh.md#warp-bypass)。 |
| 已保存的配置档 | 经过该配置档，而不是你启动的配置档。仅可在高级规则中使用。 |

同一时间只有一个路由配置档处于活动状态。内置的 `Default` 配置档只有一条 DNS 规则（`Route DNS`），并将所有流量发送到 `proxy`。

要更改活动的路由配置档，可以使用以下任一方式：

- `路由`（Routing）菜单底部的列表。活动的配置档带有勾选标记。
- 托盘菜单 → `选择路由`（Select Routing）。
- `设置`（Settings）→ `路由设置`（Routing Settings）→ `通用`（Common）→ `路由配置档`（Routing Profile），然后点击 `确定`（OK）。

在 `路由`（Routing）菜单或托盘中选择路由配置档会重启正在运行的连接，因此新规则会立即生效。当活动的路由配置档不是 `Default` 时，窗口标题会显示它的名称。

自 1.4.0 起，分组可以使用自己的路由配置档，而不是你在这里选择的那个。参见[按分组设置路由配置档](#group-routing)。

## 下载现成的配置档 {#download-profiles}

Throne 可以下载为中国、伊朗和俄罗斯维护的路由配置档。

1. 打开 `路由`（Routing）菜单 → `下载配置档`（Download Profiles），然后选择 `China`、`Iran` 或 `Russia`。
2. Throne 会列出将要添加的配置档（“添加这些远程路由配置档吗?”）。保持勾选 `自动更新`（Auto update），然后点击 `确定`（OK）。
3. 再次打开 `路由` 菜单，在底部选择其中一个新配置档。仅下载并不会更改你的活动配置档。

| 国家 | 配置档 | 作用 |
| --- | --- | --- |
| 中国 | `Bypass China` | 除中国网站、中国 IP 地址和你的本地网络走直连外，其余流量全部走代理。反审查列表中的网站始终使用代理。 |
| 中国 | `Proxy China Blocked` | 所有流量都直连，但已知在中国被封锁的网站除外。 |
| 伊朗 | `Bypass Iran` | 除伊朗网站、伊朗 IP 地址和你的本地网络外，其余流量全部走代理。 |
| 俄罗斯 | `Bypass Russia` | 除俄罗斯网站和 IP 地址、仅在俄罗斯境内可用的网站以及你的本地网络外，其余流量全部走代理。 |
| 俄罗斯 | `Proxy Russia Blocked` | 所有流量都直连，但在俄罗斯被封锁的网站和 IP 地址除外。 |
| 俄罗斯 | `Proxy Antizapret` | 所有流量都直连，但 Antizapret 封锁列表中的网站除外。 |
| 俄罗斯 | `Proxy Refilter` | 所有流量都直连，但 Re:filter 封锁列表中的网站和 IP 地址除外。 |

如果你访问的大多数网站都在境外，“Bypass”类配置档更适合你。“Proxy … Blocked”类配置档经过你服务器的流量更少，适合服务器速度较慢或有流量限制的情况。缺点是：被封锁但不在列表中的网站将无法打开。

这些配置档是互相替代的。启用你想要的那个，其余的保留即可，无需删除。它们都是[远程配置档](#remote-profiles)，因此可以自动更新。这些列表维护在 [throneproj/routeprofiles](https://github.com/throneproj/routeprofiles) 仓库中，可能会随时间变化。

如果下载失败并提示“请求配置档时出错”，请另选一个 `远程规则集镜像`（Remote Rule-set Mirror，见下文），然后重试。

## 路由配置档 {#profiles}

`设置`（Settings）→ `路由设置`（Routing Settings）会打开 `路由`（Routes）窗口，`路由`（Routing）菜单的第一项也能打开它：

| 标签页 | 内容 |
| --- | --- |
| `通用`（Common） | 活动的路由配置档、域策略和规则集镜像。 |
| `Warp` | Cloudflare WARP；参见 [WARP](@/advanced/warp.zh.md#generate)。 |
| `DNS` | DNS 服务器和选项；参见 [DNS](@/guides/dns.zh.md#settings)。 |
| `路由`（Route） | 你的路由配置档。 |

在简洁模式下，该窗口只有 `通用`（Common）和 `路由`（Route）两个标签页。此时 `通用` 标签页显示 `路由配置档`（Routing Profile），并在其下方以独立分区的形式显示 WARP 设置和 `简易 DNS 设置`（Simple DNS Settings）。域策略和 `远程规则集镜像`（Remote Rule-set Mirror）会被隐藏，但保留其值。参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)。

在 1.3.2 及更早版本中，还有一个 `劫持`（Hijack）标签页。它已在 1.4.0 中移除；参见 [DNS](@/guides/dns.zh.md#deprecated)。

`通用`（Common）标签页上的设置：

| 设置 | 默认值 | 作用 |
| --- | --- | --- |
| `路由配置档`（Routing Profile） | `Default` | 活动的路由配置档。 |
| `默认域策略`（Default Domain Strategy） | 空 | Throne 查询服务器地址或直连网站的地址时，对 IPv4 或 IPv6 的偏好。 |
| `解析域策略`（Resolve Domain Strategy） | 空 | 设置后，Throne 会在规则运行之前查询每个请求域名的 IP 地址，这样 IP 规则也能匹配域名。 |
| `远程规则集镜像`（Remote Rule-set Mirror） | `jsDelivr(Cloudflare)` | 存放在 GitHub 上的规则集和配置档从何处下载。`GitHub` 表示直接下载。 |

可选的策略有 `ipv4_only`、`ipv6_only`、`prefer_ipv4` 和 `prefer_ipv6`。在没有 IPv6 的网络上，不要选择 `ipv6_only`。

`路由`（Route）标签页上的按钮：

| 按钮 | 作用 |
| --- | --- |
| `新建`（New） | 创建 `结构化配置档`（Structured profile）、`原始配置档`（Raw profile）或 `远程配置档`（Remote profile）。 |
| `克隆`（Clone） | 复制选中的配置档。 |
| `导出`（Export） | 将选中的配置档复制为 `throne://route/` 链接（`Ctrl+C`）。 |
| `导入`（Import） | 从剪贴板添加配置档（`Ctrl+V`）。 |
| `编辑`（Edit） | 打开选中的配置档。双击该配置档效果相同。 |
| `删除`（Delete） | 删除选中的配置档（`Del`）。最后一个配置档无法删除。 |
| `更新`（Update） | `更新选定项`（Update selected）或 `更新全部`（Update all）会重新下载远程配置档。 |

路由窗口中的更改会在你点击 `确定`（OK）时保存。如果有连接正在运行，主窗口随后会显示“设置已更改，重启进行应用”。点击那里的 `重启`（Restart）即可使用新规则。

自 1.4.0 起，你也可以在 `路由`（Routing）菜单底部右键点击某个路由配置档。这会直接在编辑器中打开该配置档，无需打开 `路由设置`（Routing Settings），也不会将其设为活动配置档。如果在连接运行时保存了对活动配置档的更改，也会出现同样的提示。

路由配置档分为三种：结构化配置档，在 Throne 中通过[简单规则](#simple-rules)和[高级规则](#advanced-rules)编辑；[远程配置档](#remote-profiles)，从 URL 下载；以及[原始配置档](#raw-profiles)，以 sing-box JSON 编写。大多数人只需要结构化配置档。

创建结构化配置档的步骤：

1. 在 `路由`（Route）标签页上，点击 `新建`（New）→ `结构化配置档`（Structured profile）。
2. 输入 `名称`（Name），并选择 `默认出站`（Default outbound）。
3. 在 `基本`（Basic）标签页或 `高级`（Advanced）标签页上添加规则。
4. 点击 `确定`（OK）。在 `通用`（Common）标签页上，将该配置档选为 `路由配置档`（Routing Profile），然后点击 `确定`。

新配置档一开始带有一条 `dns-hijack` 规则。Throne 本来就会为每个结构化配置档添加同样的 DNS 步骤（参见 [DNS](@/guides/dns.zh.md#how-dns-works)），因此你可以保留这条规则不动。

自 1.4.0 起，配置档的 `DNS` 标签页可以为它设置自己的 DNS 服务器；该配置档处于活动状态时，这些服务器会取代全局服务器。参见[按路由配置档设置 DNS](@/guides/dns.zh.md#route-dns)。

当你有 OpenVPN 或 OpenConnect 配置档时，会出现 `端点`（Endpoints）标签页。它会让这些配置档与你的代理同时运行，用于它们所通告的网络；参见[分离隧道](@/advanced/vpn_profiles.zh.md#split-tunnel)。在简洁模式下，只有已经带有端点的配置档才会显示该标签页。

## 简单规则 {#simple-rules}

配置档的 `基本`（Basic）标签页中有四个输入框：`直连`（Direct）、`代理`（Proxy）、`阻止`（Block）和 `Warp-bypass`。输入框中的每一行都会把匹配的流量发送到对应的去向。`如何使用`（How to use）会显示语法的简要说明。

| 行 | 匹配 |
| --- | --- |
| `domain:example.com` | 仅 `example.com`。 |
| `suffix:example.com` | `example.com` 及其所有子域名，例如 `www.example.com`。 |
| `keyword:example` | 所有包含 `example` 的域名。 |
| `regex:^cdn[0-9]*\.example\.com$` | 匹配某个正则表达式（Go RE2 语法）的域名。 |
| `ruleset:geosite-youtube` | [规则集](#rule-sets)中的所有内容：可以是内置名称，也可以是 `.srs` 文件的 URL。 |
| `ip:10.0.0.0/8` | 某个范围内的 IP 地址。也可以写单个地址，例如 `1.2.3.4`。 |
| `processName:Telegram.exe` | 某个程序的连接，按文件名匹配。 |
| `processPath:C:\Program Files\App\app.exe` | 某个程序的连接，按完整路径匹配。 |

编写规则行时请注意：

- 每行写一个条目。空行会被忽略。
- 前缀必须与上表完全一致，包括大小写（写 `processName`，而不是 `processname`）。
- `domain:`、`suffix:` 和 `keyword:` 的值会以小写形式保存。
- 进程名必须完全匹配，包括大小写，在 Windows 上还包括 `.exe`。[连接标签页](#connections-tab)会显示确切的名称。
- 当你切换标签页或点击 `确定`（OK）时，Throne 会检查这些行，并在“无法添加某些规则”下列出无法使用的行。切换标签页会丢弃这些行。在你修正它们之前，`确定` 不会保存。自 1.4.0 起，编辑器还会在你输入时标出这些行。

{% alert_info() %}
进程规则只能看到到达 Throne 的流量。在系统代理模式下，忽略代理的程序根本不会到达 Throne，因此请将进程规则与 `Tun 模式`（Tun Mode）配合使用。参见 [TUN 模式](@/guides/tun_mode.zh.md#enable)。
{% end %}

**示例 1：除本国网站外全部走代理，并屏蔽广告。** 将 `默认出站`（Default outbound）设为 `proxy`。在 `直连`（Direct）中填写：

```text
suffix:example.com
ruleset:geosite-ir
ruleset:geoip-ir
```

在 `阻止`（Block）中填写：

```text
ruleset:geosite-category-ads-all
```

将 `ir` 替换为你所在国家的代码，例如 `cn` 或 `ru`。

**示例 2：只让一个程序和一个网站走代理。** 将 `默认出站`（Default outbound）设为 `direct`。在 `代理`（Proxy）中填写：

```text
processName:Telegram.exe
suffix:example.org
```

### 简单规则的顺序 {#simple-rules-order}

每个输入框都会变成 `高级`（Advanced）标签页上的普通规则，并以该输入框命名，例如 `Simple Address Bypass`（`直连` 中的地址）或 `Simple Process Name Proxy`（`代理` 中的程序）。在新配置档中，它们按以下顺序添加：`直连`、`阻止`、`代理`、`Warp-bypass`。由于第一条匹配的规则胜出，如果 `直连` 中范围更广的某一行已经匹配了同样的流量，`代理` 中的行就不会生效。

要设置例外，请在 `高级`（Advanced）标签页上将它上移。例如，在一个将 `geosite-ir` 设为直连的配置档中，要让 `example.ir` 走代理：

1. 将 `suffix:example.ir` 添加到 `代理`（Proxy）输入框中。
2. 打开 `高级`（Advanced）标签页，选中 `Simple Address Proxy`。
3. 点击 `上移`（Move Up），直到它位于包含 `geosite-ir` 的规则之上。

## 高级规则 {#advanced-rules}

`高级`（Advanced）标签页按顺序显示配置档的所有规则，并提供 `新建`（New）、`上移`（Move Up）、`下移`（Move Down）和 `删除`（Delete）按钮。右侧显示规则的 `名称`（Name），以及该规则的 sing-box JSON `预览`（Preview）。在简洁模式下，只有当配置档中有 `基本`（Basic）标签页无法显示的规则时（例如你自己的高级规则或已禁用的规则），才会显示该标签页。

自 1.4.0 起，列表中的每条规则都有一个复选框。取消勾选即可禁用该规则：规则仍保留在配置档中并显示为灰色，但 Throne 不会把它写入配置，就像它不存在一样。再次勾选即可重新启用。这同样适用于由 `基本`（Basic）标签页的输入框生成的规则，例如 `Simple Address Bypass`：规则被禁用期间，其中的行仍留在输入框里，但不会生效。`端点`（Endpoints）标签页的规则无法禁用。

添加规则的步骤：

1. 点击 `新建`（New）。
2. 选择一个 `动作`（Action）。
3. 打开 `+` 标签页，勾选你需要的属性。每个属性都会成为一个标签页。取消勾选某个属性会清除它的值。
4. 填写这些标签页。列表每行一个条目。

| 动作 | 作用 |
| --- | --- |
| `route` | 将匹配的流量发送到规则的 `outbound`：`proxy`、`direct`、`warp-bypass` 或某个已保存的配置档。新规则会发送到 `direct`，直到你勾选 `outbound` 并选择其他出站。 |
| `reject` | 阻止匹配的流量。`method` 和 `no_drop` 可改变阻止的方式。 |
| `hijack-dns` | 用 Throne 的 DNS 应答 DNS 查询。 |
| `route-options` | 设置 `override_address`、`override_port` 或 `tls_spoof` 等选项，然后继续执行后续规则。 |
| `sniff` | 探测连接的协议和域名，然后继续。 |
| `resolve` | 查询域名的 IP 地址（使用 `strategy`），然后继续。 |
| `bypass` | 仅限 Linux，用于高级配置。参见 [sing-box 文档](https://sing-box.sagernet.org/configuration/route/rule_action/)。 |

要在高级规则中阻止流量，请使用 `reject`。`block` 这个名称只作为默认出站和 `阻止`（Block）输入框存在。

自 1.4.0 起，`route`、`route-options` 和 `bypass` 动作还提供 `tls_fragment`、`tls_fragment_fallback_delay` 和 `tls_record_fragment`。它们会拆分匹配连接的 TLS 握手，与配置档的 [TLS 分片](@/advanced/presets.zh.md#tls-fragment)选项类似。这对走 `direct` 的连接很有帮助，例如访问某个被简单过滤器按名称拦截的网站。`tls_fragment_fallback_delay` 只能与 `tls_fragment` 一起使用。

| 属性 | 匹配内容 |
| --- | --- |
| `domain`、`domain_suffix`、`domain_keyword`、`domain_regex` | 请求的域名。 |
| `ip_cidr`、`ip_is_private` | 目标地址；`ip_is_private` 涵盖私有和本地地址范围。 |
| `rule_set` | 内置规则集名称或 `.srs` URL。 |
| `port`、`port_range` | 目标端口，例如 `443` 或 `1000:2000`。 |
| `source_ip_cidr`、`source_ip_is_private`、`source_port`、`source_port_range` | 连接的来源，例如你网络中的另一台设备。 |
| `process_name`、`process_path`、`process_path_regex` | 本机上的程序。 |
| `network`、`protocol`、`ip_version` | `tcp`、`udp` 或 `icmp`；探测到的协议，例如 `tls`、`quic` 或 `bittorrent`；`4` 或 `6`。 |
| `inbound` | 连接进入 Throne 的位置：`mixed-in`（代理端口）或 `tun-in`（TUN 模式）。 |
| `wifi_ssid`、`wifi_bssid` | 你所连接的 Wi-Fi 网络。在 macOS 上不可用。 |
| `package_name` | Android 应用。在桌面版上，带有此属性的规则永远不会匹配。 |
| `invert` | 设为 `true` 时，规则会在其条件不匹配时匹配。 |

在同一条规则中，域名、IP 和 `rule_set` 属性只要有任意一个匹配即可。其他属性（例如 `port` 或 `process_name`）也必须同时匹配。

提示：

- **例外放在前面。** 把针对单个网站的规则放在针对整个国家或整个列表的规则之上。
- **进程规则**只能看到流量到达 Throne 的程序。要包括那些忽略系统代理的程序，请使用 TUN 模式；参见[简单规则](#simple-rules)下的说明。
- **将流量发送到指定服务器。** 勾选 `outbound` 并选择一个已保存的配置档，它显示为 `[group] name`。Throne 会在你启动的配置档之外同时运行该配置档。额外核心（Extra Core）配置档和完整的自定义配置不能在此使用。
- **无法编辑的规则。** 名为“… 路由首选”（… route prefer）的规则属于 `端点`（Endpoints）标签页。你只能移动它。

## 规则集 {#rule-sets}

规则集是存放在单独文件中的域名或 IP 范围列表。可以在简单规则（`ruleset:<name>`）或高级规则的 `rule_set` 属性中使用它。

- **内置名称。** Throne 能按名称识别 2000 多个规则集。以 `geosite-` 开头的名称包含域名，例如 `geosite-youtube`、`geosite-cn` 或 `geosite-category-ads-all`。以 `geoip-` 开头的名称包含 IP 范围，例如 `geoip-ir`、`geoip-telegram` 或 `geoip-private`。当你在简单规则输入框中输入 `ruleset:`，或在 `rule_set` 标签页中输入时，Throne 会提示匹配的名称。
- **你自己的文件。** 任何 sing-box 二进制规则集的 URL 都可以使用，例如 `ruleset:https://example.com/lists/my-sites.srs`。URL 中的文件名必须包含 `.srs`。

Throne 会在配置档启动时下载该配置档的规则集，并将它们保存在核心的缓存中。`路由`（Routing）→ `更新规则集`（Update Rule-Sets）会下载最新副本。该操作在连接运行时可用，并会报告刷新了多少个规则集。

内置规则集存放在 GitHub 上。如果 GitHub 在你的网络中被封锁或速度很慢，请更改 `路由设置`（Routing Settings）→ `通用`（Common）→ `远程规则集镜像`（Remote Rule-set Mirror）。该镜像同样适用于 AdBlock 列表和存放在 GitHub 上的路由配置档。你自己的 `.srs` URL 始终按原样使用。

## 远程配置档 {#remote-profiles}

远程配置档是 Throne 从 URL 下载的结构化配置档。通过[下载配置档](#download-profiles)获得的配置档都是远程配置档。服务商也可以通过 [`throne://remoteroute/` 链接](@/advanced/deeplinks.zh.md#remoteroute)向你提供远程配置档。

自行添加远程配置档的步骤：

1. 在 `路由`（Route）标签页上，点击 `新建`（New）→ `远程配置档`（Remote profile）。
2. 输入 `名称`（Name）和 `URL`。该地址可以提供 `throne://route/` 链接、链接的 Base64 文本或配置档的 JSON。
3. 点击 `预览`（Preview）可在不做任何更改的情况下查看配置档，点击 `获取`（Fetch）可立即加载其规则。
4. 勾选 `自动更新`（Auto update）以使配置档保持最新，然后点击 `确定`（OK）。

该 URL 必须提供结构化配置档。原始配置档不能作为远程配置档。

自动更新需要同时满足两个条件：勾选配置档的 `自动更新`（Auto update）复选框，并开启 `设置`（Settings）→ `基本设置`（Basic Settings）→ `订阅`（Subscription）→ `路由配置档自动更新`（Routing profiles auto update）（仅高级模式，参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）。后者默认关闭。其间隔以分钟为单位（默认 1440，即每天一次）；小于 30 的值会将其关闭。`运行时统计`（Runtime Stats）标签页会显示 `下次远程路由更新`（Next remote route update）。要立即更新，请使用 `路由`（Route）标签页上的 `更新`（Update）。

更新会用下载的内容替换规则和默认出站，因此你对该配置档所做的更改会丢失。要安全地修改远程配置档，请先 `克隆`（Clone）它，然后打开副本并取消勾选 `自动更新`（Auto update）。更新不会重启正在运行的连接：新规则会在你下次启动配置档时使用。自 1.4.0 起，当自动更新改变了正在运行的连接所使用的路由配置档时，主窗口会显示“设置已更改，重启进行应用”。点击那里的 `重启`（Restart）即可立即使用新规则。

自 1.4.0 起，有两样东西在更新后会保留：

- 你[禁用](#advanced-rules)的规则会保持禁用，前提是下载的规则中有同名规则。这样你就可以关闭远程配置档中的某条规则，同时保持 `自动更新`（Auto update）开启。
- 配置档自己的 [DNS 设置](@/guides/dns.zh.md#route-dns)。Throne 从不从远程配置档的 URL 获取 DNS 设置。

## 按分组设置路由配置档 {#group-routing}

自 1.4.0 起，分组可以使用自己的路由配置档连接，而不是使用你在 `路由`（Routing）菜单中选择的那个。例如，某个服务商的服务器可以使用 `Bypass Iran`，而其他所有分组都使用 `Default`。

1. 右键点击该分组的标签页并选择 `编辑选定分组`（Edit selected Group），或选择 `分组`（Groups）→ `编辑当前分组`（Edit current Group）。
2. 在 `路由配置档`（Routing Profile）中选择一个配置档。默认选项 `默认（…）`（Default (…)）跟随 `路由` 菜单中的路由配置档，并显示其名称。
3. 点击 `确定`（OK）。

Throne 如何选择路由配置档：

- 由你启动的配置档所在的分组决定。对于代理链或自动选择器，看的是代理链或自动选择器本身所在的分组，而不是其中各个配置档所在的分组。前置代理、落地代理以及规则将流量发往的已保存配置档，都使用你所启动配置档的路由配置档。
- `路由`（Routing）菜单、托盘菜单以及 `路由设置`（Routing Settings）→ `通用`（Common）→ `路由配置档`（Routing Profile）为使用 `默认（…）` 的分组选择路由配置档。`路由` 菜单中的勾选标记显示的就是这个选择。
- 窗口标题和托盘提示显示正在运行的配置档所使用的路由配置档。[连接标签页](#connections-tab)修改的也是这个路由配置档的规则。
- 如果正在运行的配置档所在的分组使用自己的路由配置档，那么在 `路由` 菜单中选择其他配置档不会重启连接。日志会显示“组“…”使用自己的路由配置档，因此正在运行的配置档保持不变。”（The group "…" uses its own routing profile, so the running profile keeps it.）
- 当某个分组的配置档正在运行时，如果你更改该分组的 `路由配置档`，主窗口会显示“设置已更改，重启进行应用”。点击那里的 `重启`（Restart）。
- 当你删除某个分组正在使用的路由配置档时，该分组会恢复为 `默认（…）`，日志中也会说明。
- 分组的路由配置档也会带上它自己的 [DNS 设置](@/guides/dns.zh.md#route-dns)（如果有的话）。

### 来自订阅 {#subscription-routing}

有些服务商会随订阅一起发送一个路由配置档，格式与 Happ 应用相同。自 1.4.0 起，订阅分组可以把它保留为自己的路由配置档：

1. 按上文所述打开分组选项。
2. 在 `订阅`（Subscription）下，勾选 `使用订阅提供的路由配置档`（Use the subscription's routing profile）。
3. 点击 `确定`（OK），然后更新该订阅。

Throne 会从服务商应答中的 `routing` 标头读取路由配置档，或者从订阅中的 `happ://routing/…` 行读取。第一次更新时，Throne 会据此创建一个结构化路由配置档，使用服务商为它起的名称；如果没有名称，则以分组命名，例如名为 Provider 的分组会得到“Provider 路由”。如果该分组的 `路由配置档`（Routing Profile）是 `默认（…）`，新配置档就会成为该分组的路由配置档。在分组的列表中，它显示为“…（来自此订阅）”（… (from this subscription)）。之后的每次更新都会替换它的规则和默认出站。`日志`（Logs）标签页会显示类似“[路由] Provider：已从订阅更新路由配置档“…”（120 条，跳过 2 条）”（[Routing] Provider: updated the routing profile "…" from the subscription (120 entries, 2 skipped)）的一行。

须知事项：

- Throne 会读取直连、走代理或被阻止的网站和 IP 地址列表，以及其余流量是否走代理。这些列表会成为配置档的 `直连`（Direct）、`代理`（Proxy）和 `阻止`（Block）输入框。Throne 无法使用的条目会被跳过，并计入该日志行。
- 从不使用订阅中的 DNS 设置。该配置档遵循全局 DNS 设置，除非你在它的 `DNS` 标签页上为它设置自己的 DNS。更新会保留这些设置，也会保留它的名称以及你[禁用](#advanced-rules)的规则。
- 如果服务商发送的是 `throne://route/` 链接，Throne 会排除使用已保存配置档或 `warp-bypass` 的规则、更改目标地址的规则，以及 VPN 端点和 DNS 设置。原始配置档会被拒绝。
- 在 `路由设置`（Routing Settings）→ `路由`（Route）中，该配置档显示为“…（由订阅“…”更新）”（… (updated by subscription "…")）。你可以编辑它，但下次更新会覆盖它的规则。连接标签页不会向它添加规则（“当前路由配置档由订阅“…”更新。”，The current routing profile is updated by the subscription "…".）。要永久修改它，请 `克隆`（Clone）它，并把副本选为该分组的 `路由配置档`。副本归你自己所有；订阅只会继续更新原配置档。
- 该配置档属于这个分组。删除分组会同时删除该配置档，即使你已经取消勾选该选项。在 `路由设置` 中删除该配置档会取消勾选该选项。
- 如果订阅没有发送路由配置档，分组会保留已有的那个。
- 当 `路由设置` 处于打开状态时，更新不会更改该配置档，并会在日志中说明。下次更新会再次尝试。
- 当更新创建或更改了正在运行的配置档所使用的路由配置档时，主窗口会显示“设置已更改，重启进行应用”。

## 原始配置档 {#raw-profiles}

原始配置档是一个完整的 JSON 格式的 sing-box `route` 对象。当你需要规则编辑器未提供的 sing-box 功能时，可以使用它。

在 `路由`（Route）标签页上，点击 `新建`（New）→ `原始配置档`（Raw profile）。编辑器会检查 JSON，并提供 `格式化 JSON`（Format JSON）按钮。出站以数字表示：

| 数字 | 出站 |
| --- | --- |
| `-1` | `proxy` |
| `-2` | `direct` |
| `-5` | `warp-bypass` |
| 配置档 ID | 已保存的配置档。在 `"outbound":` 或 `"final":` 之后，编辑器会按名称提示你的配置档，并插入对应的数字。 |

如果缺少 `final`，Throne 会使用 `proxy`。内置规则集名称不会自动为你定义：请在 JSON 中自行添加 `rule_set` 条目。Throne 也不会添加结构化配置档自动获得的 DNS 步骤。对于 TUN 模式，请像这样开始编写规则：

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

`防止修改`（Prevent modifications）会完全按原样使用该对象；Throne 只会把出站数字转换为标签（tag）。此时 Throne 不会添加任何自己的内容，因此 DNS、Xray 配置档、代理链和其他功能可能会失效。只有在你非常熟悉 sing-box 时才使用它。

`启用 AdBlock (广告屏蔽)`（Enable AdBlock）、`启用 DNS 路由`（Enable DNS Routing）以及连接标签页中的规则操作都不适用于原始配置档。Throne for Android 无法使用原始配置档。

## 连接标签页 {#connections-tab}

主窗口底部的 `连接`（Connections）标签页按程序分组列出当前打开的连接。分组一开始是折叠的；点击某个程序即可查看它的连接。对于每个连接，该标签页会显示目标、协议、所用的出站及其流量。这是查看某个网站的流量去向并为其添加规则的最快方式。

自 1.4.0 起，`出站`（Outbound）列显示的是承载该连接的配置档的名称，而不是 `proxy` 之类的内部名称。对于自动选择器，它显示选择器选中的配置档；对于 WARP 这一跳，它显示 `WARP`。较长的名称会被截短；当目标需要更多空间时，该列会变窄。

自 1.4.0 起，该标签页还会显示每个连接匹配了哪条规则。将鼠标悬停在 `出站`（Outbound）列上即可查看，同时还会显示出站的完整名称，例如 `Matched: domain_suffix=github.com → My server (proxy)`。它会指出规则中匹配的那一项，例如 `domain_suffix=github.com`、`rule_set=geosite-cn` 或 `process_name=Telegram.exe`。没有单独一项可指出的规则（例如反转规则）会完整显示。`final` 表示没有规则匹配，连接使用了默认出站。

标签页右上角的按钮：`启用筛选`（Enable Filter）会在各列上方显示筛选输入框，`展开全部`（Expand All）或 `折叠全部`（Collapse All）会展开或折叠所有程序分组，`关闭下面列出的每个连接`（Close every connection listed below）会关闭列出的连接。

右键点击某个连接：

| 菜单项 | 作用 |
| --- | --- |
| `Matched: … → …` | 灰色，不可点击。显示该连接匹配的规则，以及规则将连接发往何处（自 1.4.0 起）。 |
| `Modify rules in "…"` | 一个标题，显示活动路由配置档的名称。 |
| `直连`（Direct）/ `代理`（Proxy）/ `阻止`（Block） | 打开活动路由配置档中对应输入框的目标列表（自 1.4.0 起，见下文）。 |
| `Specific server` | 让目标经由你选择的服务器（自 1.4.0 起，见下文）。 |
| `复制目标 (…)`（Copy Destination (…)）、`复制进程名称 (…)`（Copy Process Name (…)） | 复制相应的值。 |
| `关闭连接`（Close connection） | 关闭该连接。在程序上，`关闭 "…" 的所有连接 (…)`（Close all connections for "…" (…)）会关闭它的所有连接。 |
| `展开全部`（Expand All）、`折叠全部`（Collapse All） | 展开或折叠所有程序分组。 |

`直连`、`代理` 和 `阻止` 会打开一个带复选框的目标列表：

- 主机的**每一级域名**，作为 `suffix:` 规则。对于 `tile.openstreetmap.org`，目标为 `*.tile.openstreetmap.org`、`*.openstreetmap.org`，以及分隔线下方的顶级域名 `*.org`。
- **网站的名称**，作为 `keyword:` 规则，例如 `*openstreetmap*`。它还能匹配该网站的其他域名。只有名称包含 4 个或更多字母时才会提供。
- **IP 地址**，作为 `ip:` 规则，在连接没有域名时提供。
- **程序**，位于分隔线下方，作为 `processName:` 规则（`Process …`）。在程序分组上，这是唯一的目标。

已勾选的目标已经在该输入框中；取消勾选即可删除其规则。标有“(in 代理)”或类似字样的目标位于另一个输入框中：勾选它会移动该规则，因此同一条规则永远不会同时出现在两个输入框中。“(covered by … in …)”表示另一个输入框中范围更广的规则会先匹配，因此这条规则永远不会生效。将鼠标悬停在目标上可查看其规则。

点击 `确定`（OK），然后点击“设置已更改，重启进行应用”提示中的 `重启`（Restart）。新行会并入该输入框已有的简单规则。如果该输入框原本为空，其规则会被添加到列表末尾，此时位于其上方、范围更广的规则仍可能先匹配。请在 `高级`（Advanced）标签页上检查顺序。

`Specific server`（自 1.4.0 起）会打开同样的目标列表，并在其上方显示一个服务器选择框：`Server (type part of its name to search):`。在这里选择一个服务器。列表以 `[group] name` 的形式列出你的配置档，但不包括额外核心（Extra Core）配置档、完整的自定义配置以及包含它们的代理链。然后勾选应经由该服务器的目标，再点击 `确定`（OK）：

- Throne 会把这些目标放入一条高级 `route` 规则，其 `outbound` 就是该服务器。规则以服务器命名，例如域名和 IP 地址为 `My server (address)`，程序为 `My server (process)`。新规则会添加到列表末尾。
- 与输入框一样，一个目标永远不会同时出现在两个地方。勾选它会把它从某个输入框或另一个服务器的规则中移出。取消勾选会将其删除；不再包含任何目标的规则会被删除。
- 对话框打开时，会选中已经包含其中某个目标的服务器。选择其他服务器后，复选框会显示该服务器的目标。
- Throne 会在你启动的配置档之外同时运行所选服务器，与任何将流量发送到已保存配置档的规则相同（参见[高级规则](#advanced-rules)）。

当活动的路由配置档是原始配置档、被 `防止修改`（Prevent modifications）锁定、是开启了 `自动更新`（Auto update）的远程配置档，或者由[订阅](#subscription-routing)更新时（自 1.4.0 起），`直连`、`代理`、`阻止` 和 `Specific server` 会被禁用。将鼠标悬停在菜单项上可查看原因。对于远程配置档，请先按照[远程配置档](#remote-profiles)中的说明克隆它。

自 1.4.0 起，这些菜单项修改的是正在运行的配置档所使用的路由配置档，它可能是该配置档[所在分组自己的](#group-routing)路由配置档。

在 1.3.2 及更早版本中，菜单中是 `追加 "…" 到`（Append "…" to）和 `追加进程 "…" 到`（Append process "…" to）。它们只会把确切的主机或程序添加到输入框中。

该标签页需要开启 `设置`（Settings）→ `基本设置`（Basic Settings）→ `样式`（Style）→ `连接统计`（Connection statistics）→ `启用`（Enable），此项默认开启。在简洁模式下，该设置会被隐藏，但保留其值（参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）。

## AdBlock（广告屏蔽） {#adblock}

无论当前活动的是哪个路由配置档，`路由`（Routing）→ `启用 AdBlock (广告屏蔽)`（Enable AdBlock）都会在其中屏蔽广告。Throne 会为一个公开的广告屏蔽规则集（来自 217heidai/adblockfilters 项目的 `adblocksingbox`）添加一条阻止规则。该规则在你自己的 `route` 规则之前运行，因此无论是直连流量还是代理流量，广告都会被屏蔽。开启或关闭 AdBlock 会重启正在运行的连接。

- AdBlock 适用于结构化配置档和远程配置档，不适用于原始配置档。
- 如果某个网站无法正常使用，请关闭 AdBlock，检查是否是该列表屏蔽了它。
- 如果只想在某一个配置档中屏蔽广告，请改为在该配置档的 `阻止`（Block）输入框中填写 `ruleset:geosite-category-ads-all`。

## 分享配置档 {#share-profiles}

要分享路由配置档，请在 `路由`（Route）标签页上选中它，然后点击 `导出`（Export），或在列表中按 `Ctrl+C`。Throne 会复制一条 `throne://route/` 链接。你可以把这条链接发给任何人。

导入配置档：

- **在路由设置中。** 复制链接，打开 `路由`（Route）标签页，然后点击 `导入`（Import），或在列表中按 `Ctrl+V`。Throne 会询问“从剪贴板中导入 路由配置档 "…" 吗?”。如果剪贴板中没有配置档，会打开一个输入框，你可以在其中粘贴 Throne 路由链接、远程路由链接、Base64 文本或 JSON 规则数组。导入的配置档也会在 `通用`（Common）标签页上被选中，因此点击 `确定`（OK）后它就会成为活动配置档。
- **在任意位置。** 点击链接，或者复制链接后在主窗口中按 `Ctrl+V`。Throne 会询问“添加这个路由配置档吗?”，然后添加该配置档，但不会将其设为活动配置档。

链接包含以下内容：

- 名称、默认出站和规则。
- 将流量发送到已保存配置档的规则会按名称引用该配置档。在另一台电脑上，Throne 会使用同名的配置档；如果没有同名配置档，则使用 `proxy`，并告知你。
- VPN 端点，分享时不包含其凭据。导入此类链接时，Throne 会在询问“添加这个路由配置档吗?”之前先创建 OpenVPN 或 OpenConnect 配置档；即使你取消，这些配置档也会保留。
- 已禁用的规则，并带有禁用标记（自 1.4.0 起）。Throne 1.3.2 及更早版本，以及 Throne for Android 2.0.3 及更早版本，在导入链接时会排除这类规则。
- 配置档自己的 [DNS 设置](@/guides/dns.zh.md#route-dns)，如果它使用了的话（自 1.4.0 起）。在 `路由设置`（Routing Settings）中用 `导入`（Import）导入链接时，询问中会列出这些服务器：“It uses its own DNS servers: remote …, direct …”（它使用自己的 DNS 服务器：远程 …，直连 …）。该配置档处于活动状态时，你查询的每个域名都会经过这些服务器。如果链接中的设置无法使用，Throne 会将其排除，该配置档改用全局 DNS 设置。

自 1.3.2 起，如果某条规则使用了 Throne 无法保存的条件，例如网络类型或包名正则这类仅限 Android 的条件，Throne 会将其排除在外。导入前的询问会以“rule … dropped: unsupported field …”（规则 … 已被舍弃：不支持的字段 …）列出这些规则。Throne 会舍弃整条规则，因为缺少该条件时，它会匹配更多的连接。如果没有剩下任何规则，导入就会失败。

关于链接格式，请参见[深度链接](@/advanced/deeplinks.zh.md#route)。

## Android 版 {#android}

Throne for Android 拥有相同的路由配置档、规则和规则集。从侧边菜单打开 `路由`（Routing），点按某个配置档即可将其设为活动配置档。不同之处如下：

- **应用规则。** 规则编辑器中有一个 `应用`（Apps）字段，用于匹配 Android 应用。它的列表以 `未知应用`（Unknown apps）开头，用于找不到所属应用的连接（自 2.0.2 起）。在 `高级`（Advanced）下，`包名正则`（Package name regex，自 2.0.2 起）接受正则表达式，每行一个，使用 Go RE2 语法，例如 `^com\.google\.`。包名与其中任意一个表达式匹配的应用，视为已列在 `应用` 中。表达式可以匹配名称的任意部分；要匹配整个名称，请使用 `^` 和 `$`。桌面版无法识别 Android 应用，因此在桌面版上，带有应用条件的规则永远不会匹配。
- **网络规则**（自 2.0.2 起）。`网络类型`（Network type）匹配 `Wi-Fi`、`移动数据`（Mobile data）、`Ethernet` 或 `其他`（Other）。位于 `高级`（Advanced）下的 `按流量计费网络`（Metered network）只在当前网络按流量计费时匹配，例如移动数据或被标记为按流量计费的 Wi-Fi。
- **导出与导入。** 当你导出包含应用规则或网络规则的配置档时，Android 会显示一条提示。桌面版在导入链接时，会排除带有网络类型、按流量计费网络或包名正则条件的规则（参见[分享配置档](#share-profiles)）。反过来，Android 会排除使用其不支持的条件的规则，并发出警告（自 2.0.2 起）。
- **Wi-Fi 规则。** `Wi-Fi SSID` 和 `Wi-Fi BSSID` 需要将精确位置权限设为“始终允许”（Allow all the time），并开启位置信息，否则 Android 会隐藏 Wi-Fi 名称。保存此类规则时，Throne 会请求该权限。如果缺少权限，会出现“Wi-Fi rules are inactive”（Wi-Fi 规则未生效）通知。
- **原始配置档。** Android 会拒绝原始配置档的 `throne://route/` 链接，并提示“raw routing profiles are not supported on Android”（Android 不支持原始路由配置档）。来自桌面版备份的原始配置档会被保留，但无法使用，显示为“Raw (Throne desktop) · read-only”（原始配置档，来自 Throne 桌面版，只读）。
- 导入的链接中的 **VPN 端点**会被丢弃，并显示警告。
- **已禁用的规则和按配置档设置的 DNS**（自 2.0.4 起）。与桌面版一样，编辑器中的每条规则都有一个用于禁用它的复选框。编辑器中还有一个 `DNS` 卡片，提供 `使用全局 DNS 设置`（Use the global DNS settings）和 `使用自定义 DNS 设置`（Use custom DNS settings）；参见[按路由配置档设置 DNS](@/guides/dns.zh.md#route-dns)。远程配置档在更新后会保留这两者，链接中也会携带这两者。
- **`克隆`（Clone）**（自 2.0.4 起）。克隆远程配置档会得到一个永不更新的本地副本，因此你可以修改它的规则。
- **按分组设置路由配置档**（自 2.0.5 起）。分组编辑器中有 `路由配置`（Routing profile），订阅分组还有 `使用订阅提供的路由配置`（Use the subscription's routing profile）。两者的作用与桌面版相同；参见[按分组设置路由配置档](#group-routing)。原始配置档不会出现在选项中。在 `路由`（Routing）界面中，由订阅更新的配置档会显示“由订阅 "…" 更新”（Updated by subscription "…"）；长按它可查看这意味着什么。当正在运行的配置档所在的分组使用自己的路由配置时，如果你选择其他路由配置档，Throne 会提示“分组 "…" 使用自己的路由配置，因此正在运行的配置会继续使用它。”（The group "…" uses its own routing profile, so the running profile keeps it.）更改正在运行的配置档所在分组的路由配置后，请在 Throne 显示“重载代理服务以应用修改”时点按 `应用`（Apply）。
- **快速切换。** `设置`（Settings）→ `路由`（Routing）→ `当前路由配置`（Current routing profile）会打开一个对话框，列出你的路由配置档。该对话框中还有 `启用 WARP`（Enable WARP）/ `禁用 WARP`（Disable WARP）按钮。在侧边菜单的 `路由`（Routing）界面中点按某个配置档，无需对话框即可使其生效。在 2.0.4 及更早版本中，该对话框位于 `配置`（Profiles）界面的菜单（⋮）中，名为 `路由配置`（Routing profile）。
- **链接。** `throne://route/` 链接会添加配置档，但不会将其设为活动配置档。

`下载配置`（Download profiles）、`更新规则集`（Update rule-sets）和远程配置档的用法与桌面版相同。`AdBlock` 和 `自动更新远程配置`（Auto update remote profiles）位于 `设置`（Settings）→ `路由`（Routing）中。

自 2.0.2 起，即使某个规则集无法下载，连接也可以启动。Throne 会显示“无法下载规则集：…”（Could not download the rule-sets: …），并提供 `仍然连接`（Connect anyway）按钮。确认后，Throne 会在缺少这些规则集的情况下连接。使用它们的规则在 Throne 下载到它们之前不会匹配任何内容；Throne 会在连接后立即通过代理下载它们，并保留下来供以后的连接使用。

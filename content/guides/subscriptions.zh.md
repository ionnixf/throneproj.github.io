+++
title = "订阅与分组"
description = "添加和更新订阅，设置分组选项，并解决更新问题。"
weight = 10
toc = true
+++

订阅是服务商提供的一个链接，它会返回一份服务器列表。Throne 把每个订阅放在单独的分组中，也就是配置档列表上方的一个标签页，并可以替你更新它。`基本`（Basic）分组存放你自己添加的配置档；`订阅`（Subscription）分组另外带有一个 `URL`。分组类型之后无法更改。

{% alert_info() %}
更新会让订阅分组与服务商的列表保持一致：它会删除不在列表中的配置档，并撤销你对其他配置档所做的修改。请把你自己的配置档放在基本分组中。
{% end %}

`前置代理`（Front Proxy）、`落地代理`（Landing Proxy）、分组的 `高级`（Advanced）选项以及 `订阅`（Subscription）标签页上的大部分设置仅在高级模式下显示，参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)。

## 添加订阅 {#add-subscription}

1. 复制订阅地址。它以 `https://` 或 `http://` 开头。
2. 在 Throne 中按 `Ctrl+V`（`程序`（Program）→ `添加剪贴板中的配置档`（Add profile from clipboard））。
3. 选择 `创建新的订阅分组`（Create new subscription group），然后点击 `确定`（OK）。

Throne 会以地址中的主机名为分组命名并下载它。`添加配置档到这个分组`（Add profiles to this group）则只添加一次配置档，不保存地址，因此这些配置档永远不会更新。

要先设置分组名称和选项，请选择 `分组`（Groups）→ `添加新分组`（Add new Group），将 `类型`（Type）设为 `订阅`，把地址粘贴到 `URL` 中，然后点击 `确定`。创建分组并不会下载它：请右键点击它的标签页，选择 `更新订阅`（Update subscription）。

服务商提供的 `throne://addsub/…` 链接会填好名称和地址；点击 `确定` 即可添加该分组。参见[深度链接](@/advanced/deeplinks.zh.md#addsub)。

## 更新订阅 {#update}

选中分组的标签页后按 `Ctrl+U`，或者右键点击该标签页并选择 `更新订阅`。`分组` → `更新所有订阅`（Update all subscriptions）会更新每一个订阅分组，包括设置了 `跳过自动更新`（Skip automatic update）的分组。

更新会添加新的配置档、更新有变化的配置档，并删除不再出现在列表中的配置档。没有变化的配置档保留其测试结果。分组会采用服务商列表的顺序，[排过序的分组](@/guides/testing.zh.md#sort-filter)会重新排序。如果响应中完全没有配置档，分组保持不变。

- `设置`（Settings）→ `基本设置`（Basic Settings）→ `订阅` → `更新订阅前清除服务器`（Clear servers before updating subscription）会在每次更新前删除该分组的配置档，因此它们的测试结果会丢失。
- `设置` → `基本设置` → `核心`（Core）→ `Xray VLESS 首选项`（Xray VLESS Preference）在导入配置档时生效，因此更改它之后，请重新更新你的订阅。参见 [sing-box 与 Xray](@/advanced/xray.zh.md#vless-preference)。

订阅可以返回哪些格式，请参见[协议与导入格式](@/reference/protocols.zh.md#import-formats)。

### 变更报告 {#change-report}

每次更新都会向 `日志`（Logs）标签页写入一份报告：新增 `[+]`、更新 `[~]`、删除 `[-]` 和保留 `[=]` 的配置档，以及无法导入或导入时带有警告的条目和原因。手动更新单个分组后，报告还会在一个窗口中打开；要关闭这个窗口，请在 `订阅` 标签页上取消勾选 `订阅手动更新后显示变化窗口`（Show the changes window after a manual subscription update）。

### 正在使用的配置档 {#running-profile}

更新从不删除你当前连接的配置档，即使服务商移除了它。如果你希望 Throne 停止并删除它，请在 `订阅` 标签页上开启 `允许停止活动配置档`（Allow stopping the active profile）。此时，如果分组中恰好只有一个名称和类型都相同的配置档，Throne 会连接到它。

如果服务商更改了正在运行的配置档，连接会继续使用旧设置，直到你重新启动该配置档。当更新替换了正在运行的[自动选择器](@/guides/testing.zh.md#auto-selector)所使用的配置档时，它会自行重启。

## 自动更新 {#auto-update}

自动更新默认关闭。要开启它：

1. 打开 `设置` → `基本设置` → `订阅`。
2. 在 `订阅自动更新`（Subscription auto update）旁边勾选 `启用`（Enable）。
3. 设置时间间隔（分钟），至少为 30（默认值）。
4. 点击 `确定`。

自分组上次更新起经过了时间间隔后，该分组就会更新；如果更新在 Throne 关闭期间到期，则会在 Throne 启动后不久运行。设置了 `跳过自动更新` 的分组除外。

- `Use the update interval sent by the server` 会遵循服务商建议的间隔（如果服务商发送了的话）。
- 单个分组可以在其选项 → `高级`（Advanced）→ `更新`（Update）中设置自己的 `Update Interval` 和 `Server Interval`。`保持默认`（Keep Default）遵循 `订阅` 标签页，并且仍需开启 `订阅自动更新`。

## 服务商信息 {#provider-info}

许多服务商会随订阅一起发送你的套餐信息。此时，配置档列表上方的卡片会显示当前分组的这些信息：

- **流量：** 已用流量和总流量。用量达到 90 % 或套餐已到期时，进度条变为红色。
- **到期：** 剩余时间，例如 `12d left`；剩余 3 天或更少时变为红色。将鼠标悬停在上面可以查看日期。
- **更新间隔**，仅在自动更新开启时显示。
- **`Portal` 和 `Support`：** 服务商的链接。
- **公告：** 服务商的消息。点击它可以阅读全文。

将鼠标悬停在分组的标签页上会显示同样的信息。

Throne 从响应头 `Subscription-UserInfo`、`Profile-Title`、`Profile-Web-Page-Url`、`Support-Url`、`Announce` 和 `profile-update-interval` 中读取这些信息，或者从订阅开头同名的 `#` 注释行中读取。

## 分组选项 {#group-options}

右键点击分组的标签页，选择 `编辑选定分组`（Edit selected Group）。

| 设置 | 作用 |
| --- | --- |
| `前置代理`（Front Proxy）、`落地代理`（Landing Proxy） | 为该分组的每个配置档额外增加的两跳。参见[前置代理与落地代理](#front-landing-proxy)。 |
| `路由配置档`（Routing Profile） | 该分组的配置档连接时使用的路由配置档。`默认（…）`（Default (…)）跟随 `路由`（Routing）菜单。参见[按分组设置路由配置档](@/guides/routing.zh.md#group-routing)。 |
| `自动清除不可用配置档`（Auto Clear Unavailable Profiles） | 不经询问直接删除在该分组 URL 测试中失败的配置档。 |
| `跳过自动更新`（Skip automatic update） | 让该分组不参与自动更新。 |
| `使用订阅提供的路由配置档`（Use the subscription's routing profile） | 使用服务商随订阅发送的路由配置档。参见[来自订阅](@/guides/routing.zh.md#subscription-routing)。 |
| `高级`（Advanced） | [更新间隔](#auto-update)、[每次更新之后的清理](#after-update)、[User-Agent 与 HWID](#user-agent-and-hwid)、[TLS 与 HTTP 版本](#problem-web-page)和[服务器地址](#server-address)。 |

`复制配置档分享链接`（Copy profile share links）会复制分组中所有配置档的链接。

## 每次更新之后 {#after-update}

订阅分组可以在每次更新后自行清理。打开分组选项 → `高级`（Advanced）→ `更新`（Update）。所有选项默认关闭。

| 设置 | 作用 |
| --- | --- |
| `保留工作配置档`（Keep working profiles） | 当订阅不再列出上次测试成功的配置档时，仍保留这些配置档。 |
| `移除重复配置档`（Remove duplicate profiles）、`移除不安全配置档`（Remove insecure profiles）、`移除无效配置档`（Remove invalid profiles） | 与[清理](@/guides/testing.zh.md#clean-up)命令相同。 |
| `运行 URL 测试`（Run URL test） | 更新完成后测试该分组的配置档。 |
| `移除不可用配置档`（Remove unavailable profiles） | 需要开启 `运行 URL 测试`。删除测试失败的配置档。 |
| `按延迟排序`（Sort by latency） | 需要开启 `运行 URL 测试`。把最快的配置档排在最前。 |

[变更报告](#change-report)会列出被删除的配置档。只有开启了 `允许停止活动配置档` 时，你当前连接的配置档才会被删除。

## User-Agent 与 HWID {#user-agent-and-hwid}

有些服务商会为不同的应用发送不同的列表，或者拒绝它们不认识的应用。请把用户代理标识设为你的服务商所要求的值：

- 对所有分组：`设置` → `基本设置` → `订阅` → `User Agent (用户代理)标识`。留空时发送 `Throne/` 加上版本号。
- 对单个分组：分组选项 → `高级`（Advanced）→ `请求`（Request）→ `用户代理标识`（User Agent）。留空时该分组使用全局值。

有些服务商还要求提供设备 ID（HWID）。只有在你开启后，Throne 才会发送它：

- 对所有分组：在 `订阅` 标签页上勾选 `启用在更新订阅时发送硬件ID、设备型号以及 OS 版本`（Enable sending HWID, device model, and OS version when updating subscription）。
- 对单个分组：在 `请求` 下将 `发送硬件标识`（Send HWID）设为 `开启`（On）。`硬件标识`（HWID）、`操作系统`（OS）、`操作系统版本`（OS Version）和 `设备型号`（Device Model）字段会为该分组替换对应的值。

要为所有分组替换这些值，请在 `订阅` 标签页上填写 `自定义系统参数(可选)`（Custom System Parameters (optional)），并省略你想保留的值：

```text
hwid=value,os=value,osVersion=value,model=value
```

关于 Throne 发送的内容，请参见[隐私与网络请求](@/reference/privacy.zh.md#hwid)。

## 前置代理与落地代理 {#front-landing-proxy}

分组可以让其所有配置档的流量再经过两个额外的配置档：

**你的设备 → `前置代理` → 本分组中的某个配置档 → `落地代理` → WARP（如已启用）→ 互联网**

当你的设备无法直接连接该分组的服务器时，使用 `前置代理`（Front Proxy）；当你希望网站看到的是落地代理的地址，而不是该分组服务器的地址时，使用 `落地代理`（Landing Proxy）。除自动选择器外，可以选择任意已保存的配置档；在字段中输入文字即可搜索。关于代理链以及混用不同核心，请参见[代理链与自定义配置](@/advanced/chains.zh.md#front-landing)。

## 服务器地址 {#server-address}

分组可以让其所有配置档连接到另一个地址，例如 [IP 扫描器](@/guides/testing.zh.md#ip-scanner)找到的、在你的网络中可用的 CDN 地址。打开分组选项 → `高级`（Advanced）→ `Server Address`，然后选择：

- `Profiles' own addresses`：不做更改（默认）。
- `Fixed address`：一个主机名或 IP 地址，不带端口。
- `IP list`：某个 IP 列表的第一个地址。

配置档保留各自的端口，并仍会把原来的域名作为 TLS 服务器名称（SNI）和 `Host` 发送。`地址`（Address）列显示每个配置档实际使用的地址。配置档可以在其编辑器中覆盖分组的设置：`高级设置`（Advanced Settings）→ `Server Address`。

## 问题排查 {#problems}

请先打开 `日志`（Logs）标签页：每次更新都会把错误写在那里。另请参见[故障排除](@/help/troubleshooting.zh.md#subscriptions)。

### 服务商拒绝 Throne 或不发送配置档 {#problem-user-agent}

有些服务商只响应它们认识的应用。此时日志会显示诸如状态码 403 之类的错误，或者“No profiles found in the subscription”（订阅中未找到配置档）。请将该分组的 `用户代理标识`（User Agent）设为你的服务商所要求的值；用户曾经需要用到的值包括 `ClashMeta`、`sing-box` 和 `Happ/1.0`。如果服务商要求提供设备 ID，请将 `发送硬件标识`（Send HWID）设为 `开启`（On）。参见 [User-Agent 与 HWID](#user-agent-and-hwid)。

### 服务器返回网页或验证码 {#problem-web-page}

此时 Throne 会提示“The server returned a web page instead of a subscription”（服务器返回的是网页而不是订阅）。请更改 Throne 连接该服务器的方式：所有分组使用 `订阅` 标签页上的 `TLS Version` 和 `HTTP Version`，单个分组使用其 `高级` 选项中 `请求` 下的同名设置。当默认握手遇到验证码时，`TLS 1.2` 往往可以通过；也可以试试 `HTTP/1.1` 或换一个用户代理标识。

如果新分组的首次下载失败，分组会被保留。修正其设置后重新更新即可。

### “有用代理的请求，但未启动配置档。” {#problem-no-profile}

当 `设置` → `基本设置` → `杂项`（Miscellaneous）→ `使用代理`（Use proxy）或 `系统代理`（System Proxy）开启时，Throne 会通过正在运行的配置档发送它自己的请求，例如订阅更新。如果没有配置档在运行，这些请求就会失败。请先启动一个配置档，或者关闭 `使用代理` 和 `系统代理`。

### 订阅地址在你所在的国家被封锁 {#problem-blocked}

1. 启动一个可用的配置档。
2. 开启 `设置` → `基本设置` → `杂项` → `使用代理`（在简洁模式下：`通用`（Common）→ `网络设置`（Network Settings））。
3. 更新订阅。

开启 `禁用混合入站`（Disable Mixed Inbound）时，这需要 `Tun 模式`（Tun Mode）；否则更新会失败，并提示“Request with proxy but the mixed inbound is disabled and Tun Mode is off.”（有用代理的请求，但混合入站已禁用且 TUN 模式已关闭）。

### Happ 和 v2RayTun 的“crypt”链接 {#problem-crypt}

以 `happ://crypt` 或 `v2raytun://crypt` 开头的链接是专为这些应用加密的，Throne 无法读取。诸如 `…/dl/happ-link/…` 之类的网页也不是订阅。请向你的服务商索要以 `https://` 开头的普通订阅地址。

### 配置档显示为 `Custom Xray Config` {#problem-custom-config}

如果订阅返回的是完整的 Xray 配置，每个配置都会成为一个 `Custom Xray Config` 配置档，去掉 `inbounds` 后按原样运行。这是有意为之：这类配置可能包含只有作为整体才能工作的负载均衡器和代理链。

## Android 版 {#android}

Throne for Android 具有相同的分组、选项和更新。从侧边菜单打开 `分组`（Groups）。不同之处：

- **名称过滤。** 分组编辑器中的 `过滤`（Filter）选项只保留名称与 `仅保留名称匹配项`（Only names matching）匹配的配置档，例如 `HK|SG|Japan`，并删除与 `移除名称匹配项`（Remove names matching）匹配的配置档，例如 `到期|流量|官网`。两者都是正则表达式，不区分大小写，在每次更新时生效。如果过滤后一个配置档都不剩，分组保持不变。
- **信息卡片。** 在分组编辑器中关闭 `订阅信息卡片`（Subscription info card），即可隐藏该分组的[服务商信息](#provider-info)。
- **服务器地址。** Android 版没有此设置。
- **文件。** `URL` 也接受 `content://` 地址，因此订阅可以来自另一个应用提供的文件。
- **所有分组的设置**位于 `设置`（Settings）→ `订阅`（Subscriptions）。如果 `UserAgent`（User agent）为空，应用会发送 `Throne/Android/` 加上版本号。
- **`仅代理`（Proxy only）模式**会通过正在运行的配置档发送更新；没有配置档在运行时则直接发出。
- **不受支持的条目**会被跳过，参见 [Android 差异](@/reference/protocols.zh.md#android-import)。

Android 特有的问题请参见 [Android 故障排除](@/android/troubleshooting.zh.md#subscriptions)。

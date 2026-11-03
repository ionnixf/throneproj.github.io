+++
title = "测试与自动选择器"
description = "测试配置档的延迟、出口 IP 和速度，对列表进行排序和筛选，移除失效的配置档，让自动选择器挑选最佳服务器，并扫描可用的 IP 地址和 DNS 解析器。"
weight = 60
toc = true
+++

Throne 可以测试你的配置档，帮助你只保留好用的那些。本页介绍 URL 测试、IP 与国家测试、速度测试、排序与筛选、清理、会替你挑选最佳服务器的自动选择器，以及能找到在你的网络中可用的服务器地址和 DNS 解析器的 IP 扫描器。

测试不会停止正在运行的配置档。测试会跳过自动选择器配置档；请改为测试自动选择器所使用的各个分组。

## URL 测试 {#url-test}

URL 测试会检查配置档是否可用，并测量其延迟。Throne 会通过该配置档请求两次 `延迟测试 URL`（Latency Test URL），并显示第二次请求所用的时间（以毫秒为单位）。

要开始 URL 测试：

- **选定的配置档：** 右键点击它们，选择 `URL 测试选定项`（Url Test Selected，`Ctrl+Shift+S`）。
- **整个分组：** 选择 `分组`（Groups）→ `URL 测试本组`（Url Test Group，`Ctrl+Shift+G`），或者右键点击分组标签页，选择 `URL 测试选定分组`（Url Test selected Group）。
- **正在运行的配置档：** 点击状态栏左侧的配置档名称。Throne 会对当前连接测试一次，并在状态栏中显示 `测试结果:`（Test Result:）加上时间，或者显示 `不可用`（Unavailable）。

测试进行期间，右键菜单和 `分组`（Groups）菜单中会出现 `停止测试`（Stop Testing，`Ctrl+.`）。

`测试结果`（Test Result）列会显示以下内容之一：

| 结果 | 含义 |
| --- | --- |
| 时间，例如 `85 ms` | 该配置档可用。时间在 100 毫秒以内显示为绿色，300 毫秒以内为黄色，超过 300 毫秒为红色。 |
| `不可用`（Unavailable） | 测试失败。日志中会有一行包含 `测试错误`（test error）及其原因。 |
| `连接成功`（Connect OK） | OpenVPN 或 OpenConnect 配置档已连接，但测试 URL 没有通过它返回响应。 |

测试设置位于 `设置`（Settings）→ `基本设置`（Basic Settings）→ `通用`（Common）中的 `测试`（Testing）部分：

| 设置 | 默认值 | 作用 |
| --- | --- | --- |
| `延迟测试 URL`（Latency Test URL） | `http://cp.cloudflare.com/` | Throne 通过每个配置档请求的地址。 |
| `超时`（Timeout，位于 `并发数` 旁边） | `3000` 毫秒 | 每次请求的时间限制。不要设置得太低：启用多路复用的配置档在首次请求时需要更多时间，超时过短会把它们误判为失败。 |
| `并发数`（Concurrency） | `10` | 同时测试的配置档数量。 |
| `直连测试 URL`（Direct Test URL） | 空 | 不经过任何代理直接获取，使自动选择器能够分辨是互联网连接断开还是服务器失效。请使用在你的网络中无需代理即可访问的地址。留空表示 Throne 使用操作系统的网络状态。 |

自 1.4.0 起，[DNS 隧道](@/advanced/dns_tunnels.zh.md#testing)配置档的首次请求会额外获得 30 秒，MasterDNS 配置档则额外获得 90 秒，因为隧道会在这次请求时建立会话。

在简洁模式下，`测试`（Testing）部分只显示 `延迟测试 URL`（Latency Test URL）和 `Look up exit country`。本页中的其他测试设置，例如 `速度测试模式`（Speedtest mode）和 `Lookup service`，仅在高级模式下显示（参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）。

要复制选定配置档的结果，请右键点击它们，选择 `分享`（Share）→ `复制测试结果`（Copy Test Result）。

## IP 与国家测试 {#ip-test}

此测试会显示每个配置档的出口 IP 地址及其所属国家。这就是网站看到的地址。

- **选定的配置档：** 右键点击它们，选择 `解析选定的出口 IP`（Resolve Selected Out IP）。
- **整个分组：** 选择 `分组`（Groups）→ `为本组解析 IP`（Resolve out IP for group）。

这两个菜单项仅在高级模式下显示（参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）。

Throne 会通过每个配置档请求 `https://api.ip2location.io/`。之后，`测试结果`（Test Result）列会显示国旗；如果包含了 `出口 IP`（Out IP），还会显示出口 IP（参见[排序与筛选](#sort-filter)）。该测试使用 URL 测试的 `超时`（Timeout）和 `并发数`（Concurrency）设置。

国家信息会被国家筛选器以及自动选择器的 `仅指定国家`（Only countries）选项使用。关于 Throne 会联系哪些服务，请参见[隐私与网络请求](@/reference/privacy.zh.md#network-requests)。

正在运行的配置档有单独的出口查询。配置档启动后，Throne 会经由本地代理端口询问一个 IP 查询服务：该连接从哪个 IP 地址、哪个国家出口。它会在窗口左下角、窗口标题以及 `运行时统计`（Runtime Stats，仅在高级模式下显示）标签页中显示国家和城市。此查询不会改变 `测试结果`（Test Result）列。自 1.4.0 起，你可以在 `设置`（Settings）→ `基本设置`（Basic Settings）→ `通用`（Common）中的 `测试`（Testing）部分选择查询服务或关闭此查询：

| 设置 | 默认值 | 作用 |
| --- | --- | --- |
| `Look up exit country` | 开启 | 关闭：Throne 绝不会自行询问查询服务，也不显示出口国家。 |
| `Lookup service` | `Automatic` | 要询问的服务：`ip-api.com`、`ipwho.is`、`ipinfo.io`、`ipapi.co`、`IP2Location.io`、`FreeIPAPI` 或 `Cloudflare`。`Automatic` 会依次询问 `IP2Location.io`、`ip-api.com`、`ipwho.is`、`ipinfo.io` 和 `Cloudflare`，直到其中一个给出国家，20 秒后放弃。它先询问 IP2Location.io，因为 IP 与国家测试也使用它，这样两者给出的国家相同。 |

## 速度测试 {#speed-test}

速度测试会测量配置档的下载和上传速度。

- **选定的配置档：** 右键点击它们，选择 `速度测试选定项`（Speedtest Selected，`Ctrl+Shift+P`）。
- **整个分组：** 选择 `分组`（Groups）→ `速度测试本组`（Speedtest Group，`Ctrl+Alt+P`），或者右键点击分组标签页，选择 `速度测试选定分组`（Speed Test selected Group）。
- **当前连接：** 在配置档运行时，选择 `工具`（Tools）→ `速度测试当前项`（Speedtest Current，`F6`）。

Throne 会逐个测试配置档。之后，如果包含了 `速度`（Speed），`测试结果`（Test Result）列会在 `↓` 后显示下载速度，在 `↑` 后显示上传速度。

在 `设置`（Settings）→ `基本设置`（Basic Settings）→ `通用`（Common）→ `速度测试模式`（Speedtest mode）中选择测试的内容：

| 模式 | 作用 |
| --- | --- |
| `下载 + 上传`（Download + Upload，默认） | 先找到附近的 Speedtest 服务器，然后测量下载和上传速度。 |
| `仅下载`（Only Download） | 仅测量下载速度。 |
| `仅上传`（Only Upload） | 仅测量上传速度。 |
| `简单下载`（Simple Download） | 下载 `简单下载 URL`（Simple Download URL）处的文件，默认为 `http://cachefly.cachefly.net/1mb.test`。不搜索服务器。 |
| `仅国别`（Only Country） | 只查找最近的 Speedtest 服务器并显示其所在国家。速度快，几乎不消耗流量，并且可以同时测试许多配置档。 |

`速度测试模式`（Speedtest mode）旁边的 `超时`（Timeout，默认 `5000` 毫秒）限制测试中每个部分的时长。

{% alert_warning() %}
对于每个配置档，速度测试都会在超时之前尽可能多地传输数据。测试一个大型分组可能会消耗大量流量，如果你的套餐有流量限制，这一点尤其需要注意。这些数据会计入每个配置档的 `流量`（Traffic）列。
{% end %}

## 排序与筛选 {#sort-filter}

要对分组中的配置档排序：

- 点击列标题即可按该列排序。再次点击会反转顺序。Throne 会保存新的顺序。
- 首次点击按延迟排序时，最快的配置档排在最前。随后是测试失败的配置档，未测试的配置档排在最后。
- 自 1.4.0 起，分组会保持排序状态。已排序列的名称旁边有一个小箭头，表示当前的排序及其方向。测试结束、订阅更新或导入之后，Throne 会把配置档重新排好。要保留当前顺序并停止排序，请右键点击某个列标题并选择 `Stop Sorting`。
- 右键点击 `测试结果`（Test Result）列标题，可以选择该列显示的内容（`包含:`（Include:）`出口 IP`、`速度`）以及排序依据（`排序依据:`（Sort By:）`延迟`（Latency）、`下载速度(↓)`（Download Speed）、`上传速度(↑)`（Upload Speed）、`IP 出口`（IP Out））。
- 右键点击 `流量`（Traffic）列标题，可以按 `合计(Σ)`（Total）、`下载(↓)`（Downloaded）或 `上传(↑)`（Uploaded）排序。
- 拖动行可以把它们排成任意顺序。筛选开启时无法拖动。拖动行也会停止排序。

订阅更新会先把配置档排成服务商列表中的顺序，然后 Throne 再次应用分组的排序。要在每次更新后测试订阅分组并按延迟排序，请使用它的 `运行 URL 测试`（Run URL test）和 `按延迟排序`（Sort by latency）选项；参见[每次更新之后](@/guides/subscriptions.zh.md#after-update)。

要筛选列表：

1. 按 `Ctrl+F`，或点击分组标签栏右端的筛选按钮。
2. 在 `类型`（Type）、`地址`（Address）或 `名称`（Name）列标题下方的字段中输入，或在 `测试结果`（Test Result）下方的 `按国别筛选...`（Filter by country...）中输入。

之后列表只显示包含该文本的配置档；不区分大小写。在 `地址`（Address）字段中，`port=443` 显示端口为 443 的配置档，`port=1000:2000` 显示端口在 1000 到 2000 之间的配置档。范围的一端可以留空，例如 `port=8000:`。国家字段匹配来自 IP 测试或速度测试的国家代码，例如 `DE`。再次按 `Ctrl+F` 会隐藏这些字段，同时也会清空它们。

## 清理 {#clean-up}

自 1.4.0 起，这些命令位于 `分组`（Groups）→ `清理`（Clean up）中。右键点击当前分组的标签页时，也会出现同样的子菜单。它们作用于当前分组。

| 命令 | 快捷键 | 移除的内容 |
| --- | --- | --- |
| `移除重复项`（Remove Duplicates） | `Ctrl+Shift+D` | 与之前某个配置档完全相同（包括名称）的配置档。保留第一个副本。 |
| `移除不可用项`（Remove Unavailable） | `Ctrl+Shift+R` | 上次测试失败的配置档。它从不移除正在运行的配置档。 |
| `移除无效项`（Remove Invalid） | `Ctrl+Alt+I` | 被核心判定为无效而拒绝的配置档。 |
| `移除不安全的配置`（Remove Insecure Configs） | – | 流量没有得到妥善保护的配置档：未加密、TLS 不校验证书，或者使用旧的 Shadowsocks 加密方式。 |
| `清除本组测试结果`（Clear Group test result） | `Ctrl+Shift+C` | 仅清除测试结果。配置档保留。此命令直接位于 `分组`（Groups）菜单中。 |

除非开启了 `设置`（Settings）→ `基本设置`（Basic Settings）→ `样式`（Style）→ `删除配置档时跳过确认`（Skip confirmation When Deleting Profiles），否则 Throne 在删除任何内容之前都会询问。如果 `移除重复项`（Remove Duplicates）、`移除无效项`（Remove Invalid）或 `移除不安全的配置`（Remove Insecure Configs）移除了正在运行的配置档，Throne 会停止它。

要查看哪些配置档被视为不安全，请开启 `设置`（Settings）→ `基本设置`（Basic Settings）→ `样式`（Style）→ `显示配置安全信息`（Show Config Security；仅在高级模式下显示，参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）。之后 `类型`（Type）列会显示每个配置档的安全性，不安全的配置档会带有警告标志。

要自动清理：

- 分组选项 `自动清除不可用配置档`（Auto Clear Unavailable Profiles）会在该分组每次 URL 测试后，不经询问直接删除测试失败的配置档。参见[分组选项](@/guides/subscriptions.zh.md#group-options)。
- 订阅分组可以在每次更新后移除重复、不安全、无效和测试失败的配置档。参见[每次更新之后](@/guides/subscriptions.zh.md#after-update)。

## 自动选择器 {#auto-selector}

自动选择器是一种特殊的配置档。启动它后，它会从一个或多个分组中挑选最好的可用配置档，持续检查其他配置档，并在当前配置档变差或失效时切换。订阅更新带来的新服务器会自动加入其中。

要创建一个自动选择器：

1. 选中它要使用的分组的标签页。
2. 选择 `程序`（Program）→ `新建配置档`（New profile，`Ctrl+N`）。编辑器打开时，`类型`（Type）已选为 `自动选择器`（Auto Selector）。
3. 输入 `名称`（Name）。
4. 在 `服务器来自`（Servers from）中勾选自动选择器从中挑选配置档的分组。当前分组已被勾选。
5. 点击 `确定`（OK），然后像启动其他配置档一样启动这个新配置档。

编辑器会显示将会发生什么的摘要，例如已勾选的分组中有多少配置档可以使用。基本选项如下：

| 设置 | 默认值 | 作用 |
| --- | --- | --- |
| `服务器来自`（Servers from） | 当前分组 | 自动选择器使用其配置档的分组。自 1.4.0 起，这里是你的分组列表，可以勾选多个分组；它们的配置档会一起排名。至少要勾选一个分组。已归档的分组只有在被勾选时才会出现在列表中。 |
| `仅名称匹配`（Only names matching） | 空 | 可选的正则表达式。只使用名称与之匹配的配置档，例如用来挑选某个国家或服务商。可以匹配名称中的任意位置，且不区分大小写：`germany\|nl` 会使用所有包含 `germany` 或 `nl` 的名称，`^(?!.*(germany\|🇩🇪)).*$` 会使用所有既不包含 `germany` 也不包含 🇩🇪 的名称。 |
| `最优配置档间分享流量`（Share traffic between the best profiles） | 关闭 | 关闭：由一个配置档承载全部流量，其他就绪的配置档待命。开启：流量分散到多个可用的配置档上；参见下文的 `负载均衡`（Load balancing）。 |
| `首选配置档`（Preferred profile） | – | 仅当你在统计窗口中用 `使用这个配置档`（Use this profile）选择了某个配置档后才会出现。`使用自动(模式)`（Use automatic）会把选择权交还给自动选择器。 |

每个配置档的运行方式与单独启动它时相同：经由它自己所在分组的 `前置代理`（Front Proxy）和 `落地代理`（Landing Proxy）。自动选择器本身所在分组的这些代理不起作用。自动选择器的 `地址`（Address）列显示它的各个分组的名称。如果某个已勾选的分组被删除，摘要会提示这一点，下次保存时会把它从列表中移除。

`高级…`（Advanced…）会显示更多选项，分为 `要使用的配置档`（Which profiles to use）、`健康度检查`（Health checks）、`切换`（Switching）、`负载均衡`（Load balancing）和 `测试端点`（Test endpoints）几个部分。所有选项都有可用的默认值。

| 设置 | 默认值 | 作用 |
| --- | --- | --- |
| `仅指定国家`（Only countries） | 空 | 以逗号分隔的国家代码，例如 `DE,NL,FR`。自 1.4.0 起，在代码前加 `!` 则表示排除该国家：`!RU` 会使用除俄罗斯以外的所有国家，`!RU,!IR` 会使用除这两个国家以外的所有国家。使用 IP 测试或速度测试为每个配置档得出的国家，因此设置此项后，没有国家信息的配置档会被跳过，使用 `!` 代码时也是如此。正在运行的配置档的出口查询不计入。 |
| `运行最优的`（Run the best） | 300 个配置档 | 加载到运行配置中的配置档数量。自动选择器在它们之间切换时无需重新连接。 |
| `最多排名`（Rank at most） | 1000 个配置档 | 被测量并保留在排名列表中的配置档数量。替补配置档从这个列表中选取。 |
| `信任结果维持`（Trust results for） | 1440 分钟 | 不超过此时长的 URL 测试结果会被直接复用，不再重新测试。0 表示总是重新测试。 |
| `跳过失败的配置档`（Skip failed profiles） | 开启 | 在上次测试失败的结果仍被信任期间，排除这些配置档。 |
| `保持就绪`（Keep ready） | 3 个配置档 | 保持在确认可用状态的配置档数量，以便出现故障时能立即接替。 |
| `仔细检查`（Check closely） | 8 个配置档 | 在每个 `检查间隔` 内被检查的最佳配置档数量。 |
| `检查间隔`（Check interval） | 300 秒 | 被仔细检查的配置档多久测量一次。 |
| `完整扫描每隔`（Full sweep every） | 600 秒 | 对所有运行中的配置档完整检查一轮所用的时长。 |
| `监视使用中的配置档`（Watch profile in use） | 15 秒 | 单独检查承载你流量的配置档的频率。 |
| `保留样本`（Samples kept） | 10 | 排名在计算平均延迟和抖动时使用的最近检查次数。 |
| `切换容差`（Switch tolerance） | 300 毫秒 | 另一个配置档必须至少快这么多，自动选择器才会切换到它。 |
| `最大延迟`（Maximum latency） | 无限制 | 比此值更慢的配置档永远不会被选中。 |
| `故障切换尝试次数`（Failover attempts） | 2 | 当选中的配置档连接失败时，在应用看到错误之前立即尝试的其他配置档数量。 |
| `切换时断开连接`（Drop connections on switch） | 开启 | 开启：因出现问题而切换后，所有连接会立即转移到新的配置档。关闭：正在进行的下载会在旧配置档上完成。 |
| `模式`（Mode，负载均衡） | `按计时器轮换(保持会话稳定)`（Rotate on a timer (keeps sessions stable)） | 与 `最优配置档间分享流量` 配合使用。另一种模式是 `按连接(分布最广泛分散)`（Per connection (widest spread)），在该模式下，每个新连接都可以使用不同的配置档，你的出口 IP 可能会在会话过程中改变。 |
| `轮换每隔`（Rotate every） | 30 秒 | 用于计时器模式：自动选择器在一个配置档上停留多久，之后新连接会转到下一个配置档。 |
| `测试 URL`（Test URL） | 空 | 用于测量配置档的地址。留空表示使用全局的 `延迟测试 URL`。 |
| `连通性 URL`（Connectivity URL） | 空 | 不经过代理直接获取，用于发现你的互联网连接何时中断。留空表示使用测试设置中的 `直连测试 URL`。 |

运行方式：

- 当其分组中可用的配置档多于 `运行最优的`（Run the best）的值时，它会对在 `信任结果维持`（Trust results for）时限内没有测试结果的配置档进行 URL 测试，并进行排名。然后把其中最好的配置档（数量为 `运行最优的` 的值）加载到运行配置中。配置档较少时，它会一次性全部加载。日志中会显示以 `[自动选择器]`（[Auto selector]）开头的行。
- 自 1.4.0 起，它不会等待所有这些测试完成。只要有足够多的配置档可用，即达到 `仔细检查`（Check closely）的数量（默认 8 个），它就会启动。近期测试成功的配置档也计算在内。可用的配置档排在最前，其余测试继续进行，尚未测量的配置档由自动选择器自行检查。所有测试完成后，它会重新排名。如果最佳配置档发生了变化，它会按完整排名重新启动一次。
- 如果所有运行中的配置档都停止工作达 20 秒，它会改用排名其次的配置档重新构建，并且同样在其中有足够多的配置档可用时立即启动。多次重建之间的等待时间会逐渐变长，从 60 秒起，最长 10 分钟。
- 如果你的电脑没有网络连接，检查会暂停，直到网络恢复。

自动选择器运行期间，`工具`（Tools）→ `自动选择器统计`（Auto Selector Stats）会打开它的统计窗口。表格按配置档列出 `配置档`（Profile）、`状态`（Status）、`延迟`（Latency）、`抖动`（Jitter）、`检查数`（Checks）、`连接数`（Connects）、`上次通过`（Last OK）和 `备注`（Notes），其中 `状态` 为 `运作中`（Working）、`不稳定`（Unstable）、`未检查`（Not checked）、`失败中`（Failing）或 `已暂停`（Paused）之一。`仅显示有问题的配置档`（Only show profiles with problems）会隐藏正常工作的配置档。按钮如下：

- `使用这个配置档`（Use this profile）：让所选配置档保持使用，而不采用排名的选择。
- `切回自动`（Back to automatic）：让排名重新进行选择。
- `立即检查全部`（Check all now）：立即测量所有运行中的配置档。

限制：

- 自动选择器不能作为代理链中的一跳，也不能作为分组的 `前置代理`（Front Proxy）或 `落地代理`（Landing Proxy）。
- 它会跳过其各个分组中的以下配置档：代理链和其他自动选择器、OpenVPN 和 OpenConnect、Tailscale、额外核心（Extra Core）、自定义 sing-box 完整配置，以及无法读取的自定义配置。当 Xray 完整配置无法与其所在分组的前置代理或落地代理组合时，也会被跳过。

## IP 扫描器 {#ip-scanner}

自 1.4.0 起，`工具`（Tools）→ `IP Scanner`（仅在高级模式下显示，参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）会检查 IP 地址列表，并保留在你的网络中可用的地址。例如，你可以用它为服务器位于 CDN 之后的配置档找到一个未被封锁的 Cloudflare 地址，找到一个可用的 Cloudflare WARP 端点，或者为 DNS 隧道找到 DNS 解析器。扫描结果会组成一个 IP 列表，分组或配置档可以把它用作[服务器地址](@/guides/subscriptions.zh.md#server-address)。

`IP Scanner` 窗口列出你的扫描及其状态和结果数量。将鼠标悬停在某个扫描上，可以启动、暂停或继续、打开或删除它。拖动扫描可以调整顺序。关闭扫描窗口后，扫描仍会继续运行。

要创建扫描：

1. 点击 `New scan` 并输入 `Name`。
2. 选择 `Generic scan`，用 ping、TCP、TLS 或 HTTP 检查地址，也可以通过你的某个配置档进行检查。选择 `WARP scan`，可以查找能完成 WireGuard、AmneziaWG 或 MASQUE 连接的 Cloudflare WARP 端点。选择 `DNS 解析器扫描`（DNS resolver scan），可以为你的某个 DNS 隧道配置档查找解析器；参见 [DNS 解析器扫描](#dns-scan)。
3. 点击 `确定`（OK）。扫描窗口随即打开。
4. 在 `Targets` 标签页上设置目标，在 `Phases` 标签页上设置检查项。
5. 点击 `Start`。窗口会显示进度和最后几行日志，`Results` 标签页会逐渐填满。

`Targets` 标签页：

| 设置 | 默认值 | 作用 |
| --- | --- | --- |
| `Base IP list` | – | 要扫描的地址。`Manage` 会打开 [IP 列表](#ip-lists)。WARP 扫描默认使用 `Cloudflare WARP (WireGuard)`，`Use built-in WARP ranges` 会为所选 WARP 模式选择列表和端口。 |
| `Ports` | 空；WARP：WireGuard 端口 | 要扫描的端口，用逗号或空格分隔。旁边的列表决定这些端口如何与 IP 列表中保存的端口组合：`Ignore the list's ports`、`Merge with the list's ports`（默认）或 `Only the list's ports`。没有端口的地址会在 443 端口上扫描，纯 HTTP 则为 80 端口。在 WARP 扫描中，`WARP ports` 会填入 Cloudflare 端点的端口。 |
| `Shuffle the target order` | 开启 | 按随机顺序扫描地址。 |
| `Scan IPv6 addresses` | 关闭 | 关闭时，跳过列表中的 IPv6 范围。 |
| `Stop after (results)` | 无限制；WARP：10 | 结果达到此数量时扫描完成。 |
| `Concurrency` | 64；WARP：16 | 每个阶段同时检查多少个地址。 |
| `Spawn interval (ms)` | 2 | 启动两次检查之间的间隔。 |

`Phases` 标签页包含各项检查。地址必须按以下顺序通过所有已开启的检查：

| 阶段 | 默认值 | 检查内容 |
| --- | --- | --- |
| `ICMP (ping)` | 关闭 | 地址响应 ping。 |
| `TCP connect` | 开启 | 端口接受 TCP 连接。仅限 `Generic scan`。 |
| `TLS / HTTP` | 关闭 | TLS 握手成功；并且除非 `Method` 为 `None (TLS handshake only)`，HTTP 请求会得到 200 OK 响应。你可以设置 `SNI`、`Host header`、`Path`、`HTTP version`、TLS 版本、`uTLS fingerprint` 和 TLS 分片。仅限 `Generic scan`。 |
| `Config test` | 关闭 | 将所选 `配置档`（Profile）的副本指向该地址，并加载 `Test URL`。这样可以找到真正适用于你的服务器的地址，例如 CDN 地址。仅限 `Generic scan`。 |
| `WARP test` | 开启 | 通过该地址、以所选 `Mode`（`WireGuard`、`AmneziaWG` 或 `MASQUE`）建立的 WARP 连接可以工作。仅限 `WARP scan`。 |

`WARP test` 需要一个 WARP 身份（`Identity`）。默认选项 `Dedicated for this scan (recommended)` 需要点击一次 `Register`，它会向 Cloudflare 注册一个新的 WARP 设备。你也可以使用 `From a profile` 或 `Built-in WARP settings`。

`Results` 标签页显示每个地址及其 `Port` 和 `Latency`。点击列标题即可排序。按钮如下：

- `Save as IP list`：把结果复制到一个新的 IP 列表。
- `Copy`：复制选中的条目；未选中时复制全部条目。
- `Export`：把结果保存为文本文件。
- `Remove selected`：删除选中的条目。

扫描已有结果时，`Start` 会询问要做什么。`Scan from the beginning` 会清空结果并重新扫描基础列表。`Rescan current results` 只重新检查已有结果，并移除不再可用的地址。`Pause` 会保留进度，之后用 `Resume` 继续。如果更改了已暂停扫描的目标，就只能从头开始。扫描运行期间无法更改其设置。

### DNS 解析器扫描 {#dns-scan}

DNS 解析器扫描用于查找能够中继你的某个 [DNS 隧道](@/advanced/dns_tunnels.zh.md)配置档的隧道的 DNS 解析器，适用于 DNSTT（包括 VayDNS 模式）、MasterDNS，以及带 XDNS 的 VLESS (Xray)。它会把基础列表中的每个地址分别作为该配置档唯一的解析器进行尝试，并保留通过测试的解析器。

有两种创建方式：

- 在 `IP Scanner` 窗口中点击 `New scan`，选择 `DNS 解析器扫描`（DNS resolver scan）。然后在 `Phases` 标签页上选择 `配置档`（Profile）。
- 在 DNS 隧道配置档的编辑器中，点击配置档解析器下方 `IP 列表`（IP list）旁边的 `查找解析器…`（Find resolvers…）。Throne 会打开该配置档最新的 DNS 解析器扫描，或者新建一个以配置档名称加“的解析器”命名的扫描（Resolvers for）。此按钮只对已保存的配置档有效，扫描测试的是配置档最后一次保存时的状态。

在 `Targets` 标签页上，把要尝试的解析器选为 `Base IP list`，例如 `Iran DNS resolvers` 或 `Russia DNS resolvers`。DNS 解析器扫描没有 `Ports` 字段：每个地址都在列表中保存的端口上尝试，没有端口时使用 53 端口。

`Phases` 标签页只有一项检查，即 `DNS 隧道测试`（DNS tunnel test）：

| 设置 | 作用 |
| --- | --- |
| `配置档`（Profile） | 解析器必须中继其隧道的 DNS 隧道配置档。列表中只显示 DNSTT、MasterDNS 和 XDNS 配置档。 |
| `测试`（Test） | 要检查的内容。可选项取决于配置档的类型，见下文。 |
| `超时 (毫秒)`（Timeout (ms)） | 测试单个解析器的总时限，范围为 1000 到 300000 毫秒。留空表示使用所选测试的默认值，空字段中会显示该值。 |

各项测试按从最快到最全面排列。对于每种配置档，每项测试都会同时完成排在它之前的测试的检查：

| 测试 | 配置档 | 默认超时 | 检查内容 |
| --- | --- | --- | --- |
| `轮询：中继一个隧道查询`（Poll: relays one tunnel query） | DNSTT、XDNS | 15000 毫秒 | 解析器把一个隧道查询转发给隧道服务器，并返回有效的应答。 |
| `查询名：还能中继与配置档所发送的同样长的名称`（Query names: also relays names as long as the profile sends）（默认） | DNSTT、XDNS | 90000 毫秒；XDNS：60000 毫秒 | 解析器还能中继与配置档所发送的同样长的查询名。扫描会找出该解析器能中继的最长名称。 |
| `握手：还能建立隧道会话`（Handshake: also opens a tunnel session） | DNSTT | 120000 毫秒 | 能通过该解析器与服务器建立隧道会话。 |
| `快速：中继最小的数据包`（Quick: relays the smallest packets）（默认） | MasterDNS | 20000 毫秒 | 解析器能中继配置档所需的最小尺寸的查询和应答。 |
| `完整：还会测量上传和下载 MTU`（Full: also measures the upload and download MTU） | MasterDNS | 90000 毫秒 | 扫描还会找出该解析器能中继的最大查询和最大应答。 |

在 `Results` 标签页上，`Latency` 是解析器的应答用时：对于 DNSTT 和 XDNS 是第一个隧道查询的用时，对于 MasterDNS 是在所找到的尺寸下的平均往返时间。DNS 解析器扫描会增加以下列，只有当至少一个结果有对应的值时才会显示该列：

| 列 | 含义 |
| --- | --- |
| `最大 QNAME`（Max QNAME） | DNSTT 和 XDNS：解析器能中继的最长查询名，DNSTT 使用 `最大 QNAME 长度`（Max QNAME Length）的单位，XDNS 使用 `lenLimit` 的单位。 |
| `上传 MTU`（Upload MTU）、`下载 MTU`（Download MTU） | MasterDNS：解析器在每个查询和每个应答中能中继的最大有效载荷。 |

未通过的解析器不会保留。日志会显示每个解析器以及它停下的阶段：DNSTT 和 XDNS 为 `poll`、`qname` 或 `handshake`；MasterDNS 为 `timeout`（没有应答）、`decode`（应答无法读取）、`upload` 或 `download`（没有任何数据包尺寸通过）。

要把结果设为配置档的解析器，请在 `Results` 标签页上点击 `用作解析器`（Use as resolvers）。扫描的结果列表随即成为该扫描所用配置档的 `IP 列表`（IP list）。配置档中手动填写的解析器会保留，并优先使用。正在运行的配置档会重新启动。如果该配置档的编辑器已打开，列表会改为填入编辑器；在编辑器中点击 `确定`（OK）即可保存。

配置档每次启动时都会读取该列表，因此总是使用最新的结果。`Scan from the beginning` 会清空列表，`Rescan current results` 会移除不再可用的解析器。如需固定不变的一组解析器，请点击 `Save as IP list`，再把新列表选为配置档的 `IP 列表`。

### IP 列表 {#ip-lists}

在 `IP Scanner` 窗口中点击 `IP Lists`，即可管理扫描所读取、分组和配置档可以使用的列表。Throne 自带七个列表：`Cloudflare CDN`、`Fastly CDN`、`Gcore CDN`、`Cloudflare WARP (WireGuard)`、`Cloudflare WARP (MASQUE)`，以及 `Iran DNS resolvers` 和 `Russia DNS resolvers`，后两个列表包含在这些国家境内可以访问的解析器，供 [DNS 解析器扫描](#dns-scan)使用。如果删除了它们，`Import` → `Add default lists` 会重新添加。

- `New` 创建一个列表。其 `Source` 为 `Manual`（由你填写条目）、`URL`（Throne 下载的 http 或 https 地址）或 `Rule-set`（某个规则集的 IP 范围，例如 `geoip-ir`，或规则集 URL）。对于 `URL` 或 `Rule-set` 列表，`Fetch now` 会立即下载，`Auto update` → `Enable` 会每隔 `Every` 中设定的分钟数刷新一次，至少 30 分钟。
- 每行写一个条目，例如 `1.2.3.4`、`1.2.3.0/24`、`1.2.3.4:443`、`[2606:4700::1]:443`、`1.2.3.0/24 443` 或 `1.2.3.1-1.2.3.9`。
- `Import` 可以 `From text…`、`From file…`（文本和 CSV 文件，或 sing-box 规则集，每个最大 50 MB）、`From URL…` 或 `From rule-set…` 创建列表。
- `Duplicate` 复制列表，`Export` 把列表保存为文本文件。

每个扫描的结果也是一个列表，标有“Result of scan”和扫描名称。分组或配置档可以直接使用它，但 `Scan from the beginning` 会清空它；如需内容不变的列表，请使用 `Save as IP list`。删除扫描时，你可以选择是否同时删除其结果列表。

## Android 版 {#android}

Throne for Android 提供相同的测试和相同的自动选择器。

- 在 `配置`（Profiles）界面上，`⋮` 菜单为当前分组提供 `测试`（Test），其中有 `URL 测试`（URL test）、`IP 与国家测试`（IP & country test）、`速度测试`（Speed test）和 `停止测试`（Stop testing）。在 2.0.5 之前，这些项直接位于 `⋮` 菜单中。在 2.0.3 中，该菜单还有 `测试当前连接的速度`（Speed test current connection）；自 2.0.4 起，它位于长按屏幕底部横栏时打开的菜单中。`⋮` 菜单还提供 `解析域名`（Resolve domains）、`清理测试结果`（Clear test results）、`清空流量统计数据`（Clear traffic statistics）、`删除`（Remove）→ `重复项`（Duplicates）/`不可用`（Unavailable）/`无效`（Invalid）/`不安全配置`（Insecure configs），以及 `排序`（Sort）。对于选中的配置档，请使用选择菜单中的 `测试`（Test）。
- 配置档的 `⋮` 按钮提供针对该配置档的 `测试`（Test）。自 2.0.5 起，它还提供 `清理测试结果`（Clear test results）和 `清空流量统计数据`（Clear traffic statistics），只清除这一个配置档的相应数据，且不会询问确认。只有当配置档有测试结果或流量时，对应的项才会出现。
- 在对多个配置档进行速度测试之前，应用会请求确认：“速度测试可能消耗大量流量。”
- 测试面板会显示进度、`可用`（Working）、`失败`（Failed）、`等待中`（Pending）和 `已跳过`（Skipped）的计数、延迟图表（≤100 ms、≤300 ms、>300 ms、fail）、最快的配置档、各个国家以及不同出口 IP 的数量。它还提供一些操作，例如 `按延迟排序`（Sort by latency）或 `按速度排序`（Sort by speed）、`删除不可用`（Remove unavailable，附带失败配置档的数量）、`连接最快的`（Connect to fastest；未连接时为 `选择最快的`（Select fastest））以及 `关闭测试结果`（Dismiss test results）。
- 连接后，点按统计栏（“已连接 , 点击此处测试连接”）即可测试当前连接。结果类似于“连接成功: HTTP 握手耗时 120ms”或“失败: …”。自 2.0.3 起，结果显示为“HTTPS：120 毫秒”或“HTTP：120 毫秒”。
- 自 2.0.2 起，统计栏还会显示当前连接的出口 IP 地址和国旗，竖屏时还会显示国家和城市。在 2.0.2 中，长按统计栏可查看完整信息。Throne 会在连接后以及每次点按统计栏后，通过代理询问一个 IP 查询服务；在 2.0.2 中，该服务始终是 `ip-api.com`。自 2.0.3 起，统计栏只显示国旗和国家，长按统计栏则会打开 `实时图表`（Live graphs）（自 2.0.4 起：打开包含 `实时图表` 和 `测试当前连接的速度` 的菜单）。
- 自 2.0.3 起，`设置`（Settings）→ `测试`（Testing）的 `Exit country` 部分提供 `Look up exit country`（默认开启）和 `Lookup service`，可选的服务与桌面版相同。关闭查询后，统计栏不显示国家。在 2.0.3 中，`Automatic` 会依次询问 `ip-api.com`、`ipwho.is`、`ipinfo.io` 和 `Cloudflare`；自 2.0.4 起，它会像桌面版一样先询问 `IP2Location.io`。
- Throne for Android 没有 IP 扫描器。自 2.0.3 起，在 `排序`（Sort）中选择的排序会保持，与桌面版相同，`手动排序`（Manual order）会停止排序。
- 要创建自动选择器，请选择 `添加服务器配置`（Add profile）→ `手动输入`（Manual settings）→ `自动选择`（Auto selector）。选项和默认值与桌面版相同，只是英文名称采用句首大写的写法，例如 `服务器来源`（Servers from）、`仅限名称匹配`（Only names matching）和 `运行前`（Run the best）。自 2.0.3 起，`服务器来源` 会打开一个列表，你可以在其中勾选一个或多个分组。自 2.0.5 起，`仅限国家`（Only countries）也接受 `!` 代码，并且自动选择器会像桌面版一样，在有足够多的配置档可用时立即启动。在 `配置`（Profiles）界面上点击自动选择器的状态行，即可打开 `自动选择统计`（Auto selector stats），其中有 `使用此配置`（Use this profile）、`恢复自动`（Back to automatic）和 `立即全部检查`（Check all now）。

+++
title = "隐私与网络请求"
description = "Throne 会向订阅服务商发送哪些信息、应用自行发出的所有网络请求，以及谁可以使用它的本地代理端口。"
weight = 40
toc = true
+++

Throne 会按照你的配置档和路由规则发送你的流量。本页介绍 Throne 自行发送的内容：用于订阅的设备信息（HWID）、应用自行发出的网络请求，以及谁可以使用它的本地代理端口和 Throne API。Throne 不会发送使用统计，也不会上传日志或崩溃转储。

## HWID 与设备信息 {#hwid}

有些服务商想知道是哪台设备在请求订阅。Throne 可以在订阅请求中附带四个请求头。此功能默认关闭。

| 请求头 | Windows | Linux | macOS |
| --- | --- | --- | --- |
| `x-hwid` | Windows 的 `MachineGuid` | `/etc/machine-id`（或 `/var/lib/dbus/machine-id`）的内容。两者都没有时，为 Throne 创建一次的 ID（自 1.4.0 起） | Mac 的硬件 UUID |
| `x-device-os` | `Windows` | `Linux` | `macOS` |
| `x-ver-os` | Windows 版本，例如 `10.0.26100` | 内核版本 | macOS 版本，例如 `14.5` |
| `x-device-model` | Windows 报告的电脑型号（及主板） | 发行版名称，例如 `Ubuntu 24.04.1 LTS` | macOS 名称和版本，例如 `macOS Sonoma (14.5)` |

开启方法：

1. 打开 `设置`（Settings）→ `基本设置`（Basic Settings）→ `订阅`（Subscription）。
2. 开启 `启用在更新订阅时发送硬件ID、设备型号以及 OS 版本`（Enable sending HWID, device model, and OS version when updating subscription）。此设置及下文提到的其他设置仅在高级模式下显示（参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）。
3. 点击 `确定`（OK）。

将鼠标悬停在该复选框上，可以查看 Throne 在你的电脑上获取到的值。

**Linux：** 自 1.4.0 起，在没有有效机器 ID 的系统上（例如容器中），Throne 会生成一次随机 ID，并将其保存在配置文件夹中的 `hwid` 文件里（只有你可以读取），使该 ID 保持不变。如果你删除该文件，Throne 会创建一个新的 ID。如果它无法写入该文件，就不会发送 ID。关于无界面 Throne，另请参见[设备 ID](@/headless/_index.zh.md#device-id)。

- Throne 只在订阅请求中发送这些请求头，绝不会在其他任何请求中发送。
- `自定义系统参数(可选)`（Custom System Parameters (optional)）可以替换 Throne 发送的值。请使用 `hwid=value,os=value,osVersion=value,model=value` 的格式，并且只列出你想替换的值。
- 每个订阅分组都可以单独决定：选择 `分组`（Groups）→ `编辑当前分组`（Edit current Group），然后在 `订阅`（Subscription）部分点击 `高级`（Advanced）。`发送硬件标识`（Send HWID）可以是 `保持默认`（Keep Default，即沿用上面的设置）、`开启`（On）或 `关闭`（Off）。`硬件标识`（HWID）、`操作系统`（OS）、`操作系统版本`（OS Version）和 `设备型号`（Device Model）字段只替换该分组的值。
- 每个订阅请求还会发送 User-Agent：`Throne/<version>`，例如 `Throne/1.3.1`，除非你在同一标签页中或为该分组设置了其他 `User Agent`。

关于服务商何时需要这些设置，参见 [User-Agent 与 HWID](@/guides/subscriptions.zh.md#user-agent-and-hwid)。

**Android：** `设置`（Settings）→ `订阅`（Subscriptions）中有相同的开关（默认关闭）以及 `自定义 HWID 参数（可选）`（Custom HWID parameters (optional)）。Throne for Android 将 Android ID（`ANDROID_ID`）作为 HWID 发送。如果无法获取 Android ID，它会生成一次随机 ID，并在之后一直发送这个 ID。它会将 `Android` 作为操作系统发送，同时发送 Android 版本和设备型号。它的默认 User-Agent 为 `Throne/Android/<version>`。

## 网络请求 {#network-requests}

除了你的应用产生的流量之外，Throne 还会发出下表中的请求。其中许多请求遵循“`使用代理`（Use proxy）规则”：

- 默认情况下，它们直接发出。
- 如果开启了 `设置`（Settings）→ `基本设置`（Basic Settings）→ `杂项`（Miscellaneous）→ `使用代理`（Use proxy），或开启了 `系统代理`（System Proxy），它们会改为经由 Throne 的本地代理端口发出。在简洁模式下，`使用代理` 位于 `通用`（Common）标签页的 `网络设置`（Network Settings）部分。
- 如果它们应当使用本地代理端口，但没有配置档在运行，请求就会失败，并提示“有用代理的请求，但未启动配置档。”
- 自 1.4.0 起，如果开启了 `禁用混合入站`（Disable Mixed Inbound），就没有本地代理端口。TUN 模式开启时，本应使用该端口的请求会像其他应用的流量一样发出，并由 TUN 模式路由。TUN 模式关闭时，请求会失败，并提示“Request with proxy but the mixed inbound is disabled and Tun Mode is off.”（请求需要经过代理，但混合入站已禁用且 TUN 模式未开启。）

经由本地代理端口发出的请求，会像其他任何应用的流量一样进入 Throne，因此由你的路由配置档决定它的去向。

| 请求 | 发出时机 | 目标 | 路由方式 |
| --- | --- | --- | --- |
| 订阅更新 | 添加或更新订阅时；如果开启了 `订阅自动更新`（Subscription auto update），还会定时进行（该选项默认关闭） | 你的服务商的 URL | `使用代理` 规则 |
| 远程路由配置档 | 添加或更新远程路由配置档时；如果开启了 `路由配置档自动更新`（Routing profiles auto update），还会定时进行（该选项默认关闭） | 该配置档的 URL。位于 `raw.githubusercontent.com` 上的地址会经由 `远程规则集镜像`（Remote Rule-set Mirror）访问。 | `使用代理` 规则 |
| 路由配置档列表 | 选择 `路由`（Routing）→ `下载配置档`（Download Profiles）→ 某个国家时 | GitHub 上的 throneproj/routeprofiles 仓库，经由镜像访问 | `使用代理` 规则 |
| 规则集 | 配置档启动时，如果其路由或 `启用 AdBlock (广告屏蔽)`（Enable AdBlock）需要一个尚未存入 `cache.db` 的规则集；配置档运行期间大约每天一次；以及选择 `路由` → `更新规则集`（Update Rule-Sets）时 | 经由 `远程规则集镜像`（默认为 `jsDelivr(Cloudflare)`）访问 GitHub，或你添加的 `.srs` URL | 由核心通过你的路由配置档的默认出站发送（如果默认出站为 `block`，则直接发送） |
| 出口国家查询 | 每次启动配置档后，以及在配置档运行期间打开 `运行时统计`（Runtime Stats）标签页时。自 1.4.0 起，仅在开启 `Look up exit country`（默认开启）时进行。 | `Lookup service`。选择 `Automatic`（默认）时，先查询 `https://api.ip2location.io/`，然后依次查询 `http://ip-api.com/json/`、`https://ipwho.is/`、`https://ipinfo.io/json` 和 `https://www.cloudflare.com/cdn-cgi/trace`，直到其中一个给出国家 | 始终经由本地代理端口 |
| URL 测试 | 运行 URL 测试或点击状态栏时、打开 `运行时统计` 标签页时、自动选择器运行期间，以及分组开启了 `运行 URL 测试`（Run URL test）时在订阅更新之后 | `延迟测试 URL`（Latency Test URL），默认为 `http://cp.cloudflare.com/` | 经由每个被测试的配置档 |
| IP 测试 | 仅在选择 `解析选定的出口 IP`（Resolve Selected Out IP）或 `为本组解析 IP`（Resolve out IP for group）时 | `https://api.ip2location.io/` | 经由每个被测试的配置档 |
| 速度测试 | 仅在运行速度测试时 | Speedtest.net 服务器列表和附近的一台 Speedtest 服务器。在 `简单下载`（Simple Download）模式下，则为 `简单下载 URL`（Simple Download URL），默认为 `http://cachefly.cachefly.net/1mb.test`。 | 经由每个被测试的配置档 |
| 连通性检查 | 自动选择器运行期间，且仅当你设置了 `直连测试 URL`（Direct Test URL）或 `连通性 URL`（Connectivity URL）时（两者默认均为空） | 该 URL | 直连 |
| 域名解析 | 仅在选择 `为本组解析域名`（Resolve Domain for group）或 `解析选定域名`（Resolve Selected Domain）时 | 对你的服务器域名进行 DNS 查询，发往你的 [DNS 设置](@/guides/dns.zh.md#settings)中的 `直连 DNS`（Direct DNS）服务器（默认为 `localhost`，即系统的解析器） | 自 1.4.0 起由核心直接发送，从不经过代理。此前，Throne 会询问你的系统 DNS 解析器。 |
| 更新检查 | 仅在选择 `程序`（Program）→ `检查更新`（Check For Update）时（1.4.0 之前为 `工具`（Tools）→ `检查更新`）。只有点击 `更新`（Update）后才会开始下载。在 macOS 上、使用系统 Qt 的软件包中，以及通过 Linux 安装脚本安装的副本中，该菜单项处于禁用状态。 | `api.github.com`：发布列表，以及（自 1.4.0 起）有新版本时该版本的 `CHANGELOG.md`，用于在更新窗口中显示更新日志。下载的是 GitHub 上的发布文件。 | `使用代理` 规则 |
| WARP 注册 | 仅在生成 WARP 配置或身份时 | `api.cloudflareclient.com`，或你在 `注册域名...`（Registration Domains…）中设置的域名 | `使用代理` 规则 |
| Xray geo 文件 | 仅当某个 Xray 配置需要 `geoip.dat` 或 `geosite.dat` 且你同意下载时、你点击 `下载`（Download）时，或 Throne API 客户端请求下载时 | `GeoIP 资源 URL`（GeoIP Asset URL）和 `GeoSite 资源的 URL`（GeoSite Asset URL），默认指向 GitHub | `使用代理` 规则 |
| IP 扫描 | 仅在你于 `工具`（Tools）→ `IP Scanner` 中开始扫描时（自 1.4.0 起） | 先与公共 DNS 服务器（例如 `1.1.1.1:443` 和 `8.8.8.8:443`）建立 TCP 连接以检查网络；然后是你扫描的 IP 列表中的地址 | 直连，经由你的网卡，TUN 模式开启时也是如此 |
| IP 列表更新 | 更新带有 URL 或规则集来源的 IP 列表时；如果你为该列表开启了自动更新（默认关闭），还会定时进行（自 1.4.0 起） | 该列表的 URL；内置列表来自 GitHub 上的规则集，经由 `远程规则集镜像`（Remote Rule-set Mirror）访问 | `使用代理` 规则 |
| 网络仪表盘 | `工具` → `打开网络仪表盘`（Open Web dashboard），仅当你的版本没有内置仪表盘时。官方版本都内置了仪表盘。 | GitHub | 始终经由本地代理端口 |
| WebDAV 备份 | 仅在你点击 `Upload to WebDAV` 或 `Restore from WebDAV...`、在该列表中恢复或删除备份，或在 `WebDAV Settings...` 中点击 `Test` 时（自 1.4.0 起）。不会自动上传。 | 你输入的 `Server URL` 中的 `Folder`（默认为 `Throne`）。上传会发送一个备份文件，并更新该文件夹中的备份列表 `throne_backups.json`。 | `使用代理` 规则。开启 `Connect through the proxy` 时，始终经由本地代理端口。 |
| NTP | 仅当你开启 `NTP 设置`（NTP Settings）时 | 你输入的 NTP 服务器 | 你选择的 `出站`（outbound），默认为 `direct` |

在简洁模式下，`IP Scanner`、`打开网络仪表盘`（Open Web dashboard）、解析域名和出口 IP 的菜单项、`运行时统计` 标签页以及 `NTP 设置`（NTP Settings）都会被隐藏（参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）。此前所做的设置仍然有效，因此 IP 列表的定时更新和 NTP 会继续工作。

关于出口国家查询：

- Throne 用它在状态栏、窗口标题和 `运行时统计`（Runtime Stats）标签页中显示你的连接所在的国家和城市。
- 它始终经由本地代理端口。使用内置的 `Default` 路由配置档时，它会通过你的代理发出，因此查询服务看到的是你的代理地址，而不是你自己的地址。
- 自 1.4.0 起，`设置` → `基本设置` → `通用`（Common）→ `Look up exit country` 可以关闭它，`Lookup service` 用于选择服务。参见 [IP 与国家测试](@/guides/testing.zh.md#ip-test)。
- 对 ip-api.com 使用明文 HTTP，对其他服务使用 HTTPS。Throne 会发送它的 User-Agent。
- 启动之后，如果正在运行的配置档的出口是 OpenVPN 或 OpenConnect 配置档，Throne 会跳过此查询。

关于 WebDAV 备份：

- 备份不会加密。它包含在 `创建备份`（Create Backup）下勾选的部分，例如你的服务器密码和订阅链接。备份列表还会记录设备名称（除非你另行设置，否则为电脑的名称）、一个随机的设备 ID、平台和 Throne 版本。
- `Username` 和 `Password` 通过 HTTP 基本认证（basic authentication）发送给服务器，使用 `http://` 时不会加密。Throne 将它们保存在本机的设置中，绝不会放入备份。
- 参见 [WebDAV 备份](@/guides/backup.zh.md#webdav)。

自 1.3.2 起，更新检查和更新下载始终会验证 GitHub 的 TLS 证书，即使开启了 `基本设置` → `杂项` → `忽略 TLS 错误`（Ignore TLS errors）也是如此，因为发布文件没有其他签名。

来自 [Throne API](@/advanced/api.zh.md) 客户端的请求（例如订阅更新、从 URL 导入和 geo 文件下载）与上述请求相同，并遵循相同的路由方式。这也包括 `thronectl` 的 Web 界面（`thronectl web`）：它的网关只与 Throne API 通信，它的页面不会从其他网站加载任何内容。[无界面 Throne](@/headless/_index.zh.md) 不进行出口国家查询，并且会在不询问的情况下下载缺少的 Xray geo 文件。

你的应用流量所产生的 DNS 查询，会发往 `路由设置`（Routing Settings）→ `DNS` 中设置的服务器；自 1.4.0 起，如果当前路由配置档有自己的 DNS 设置，则发往它的服务器（参见[按路由配置档设置 DNS](@/guides/dns.zh.md#route-dns)）。参见 [DNS](@/guides/dns.zh.md)。

**Android：** Throne for Android 会为订阅、路由配置档、规则集、测试和 WARP 注册发出同类请求。WebDAV 备份只发往你在 `WebDAV 设置`（WebDAV settings）中设置的服务器，并且只在你备份、恢复或测试连接时发出；自 2.0.4 起，删除备份时也会发出。这些请求从不使用本地代理。自 2.0.4 起，上传时还会像桌面版一样在备份列表中记录设备名称、一个随机的设备 ID、平台和版本。开启 `设置`（Settings）→ `订阅`（Subscriptions）→ `使用代理`（Use proxy）时，或处于 `仅代理`（Proxy only）模式时，只要有配置档在运行，它自身的请求就会使用本地代理。如果没有配置档在运行：开启了 `使用代理` 时（在 2.0.3 及更早版本中，处于 `仅代理` 模式时也是如此），订阅更新和 WARP 注册会失败，并提示“请求使用代理但未启动配置。”（Request with proxy but no profile started.）；自 2.0.4 起，仅处于 `仅代理` 模式时，这些请求会直接发出。更新检查、路由配置档下载和规则集列表在没有配置档时也始终可用：它们会直接发出。自 2.0.2 起，Throne for Android 在每次连接后以及从底栏进行连接测试后，也会查询你的出口位置；该请求经由本地代理发出，在关闭混合入站时则经由 VPN 发出。在 2.0.2 中，它始终查询 `http://ip-api.com/json/`。自 2.0.3 起，`设置` → `测试`（Testing）→ `Look up exit country` 可以关闭此查询，`Lookup service` 提供与桌面版相同的服务。`Automatic` 在 2.0.3 中先查询 ip-api.com，自 2.0.4 起先查询 IP2Location.io。同样自 2.0.2 起，当带有 `geoip:` 或 `geosite:` 规则的 Xray 配置启动或被测试时，它会从 GitHub 下载 `geoip.dat` 和 `geosite.dat`（默认来自 Loyalsoldier）。它只在你点按 `检查更新`（Check for updates）时，或在你开启 `每天检查更新`（Check for updates daily，该选项默认关闭）后每天一次，向 GitHub 检查更新。自 2.0.4 起，更新界面还会从 `api.github.com` 下载新版本的更新日志。`路由`（Routing）界面上的 `刷新仓库列表`（Refresh repository list）会从 GitHub 下载路由配置档列表。

## 本地代理端口 {#local-proxy}

配置档运行期间，Throne 会监听一个本地代理端口，它同时接受 SOCKS5 和 HTTP 连接。该端口默认为 `127.0.0.1:2080`。只有你自己电脑上的程序才能访问 `127.0.0.1`，但其中任何程序都无需密码即可使用该端口。

相关设置位于 `设置`（Settings）→ `基本设置`（Basic Settings）→ `通用`（Common）→ `入站设置`（Inbound Settings）：

| 设置 | 默认值 | 作用 |
| --- | --- | --- |
| `监听地址`（Listen Address） | `127.0.0.1` | 设为 `::` 或 `0.0.0.0` 会向其他设备开放该端口。 |
| `监听端口`（Listen Port） | `2080` | 端口号。 |
| `随机端口`（Random port） | 关闭 | 每次启动时使用一个空闲的随机端口。 |
| `启用认证`（Enable Authorization） | 关闭 | 要求提供 `入站用户名`（Inbound Username）和 `入站密码`（Inbound Password）。 |
| `禁用混合入站`（Disable Mixed Inbound） | 关闭 | 关闭该端口。此时无法使用 `系统代理`（System Proxy）。 |

`程序`（Program）菜单和托盘菜单中的 `允许其他设备连接`（Allow other devices to connect）会把 `监听地址`（Listen Address）设为 `::`。此后，任何能访问你电脑的设备都可以使用你的代理。这样做时，请开启 `启用认证`（Enable Authorization）。参见[局域网共享](@/guides/proxy_modes.zh.md#lan-sharing)。

其他本地端口：

| 端口 | 默认值 | 谁可以访问 |
| --- | --- | --- |
| `DNS 服务器端口`（DNS Server Port）：`基本设置` → `杂项` | `5533` | 仅限本机（`127.0.0.1`）。自 1.4.0 起，其下方的 `允许其他设备连接`（Allow other devices to connect）可向你的网络开放该端口；参见[供其他设备使用的 DNS 服务器](@/guides/dns.zh.md#dns-server)。 |
| Clash API：`基本设置` → `核心`（Core） | 关闭（`监听端口` 为空） | Clash API 的 `监听地址`，默认为 `127.0.0.1`。开启时请设置 `密钥`（Secret）。 |
| sing-box API / 仪表盘：`基本设置` → `核心` | 关闭（`监听端口` 为空） | 仅限本机。由随机生成的 `密钥` 保护。 |
| Throne API：`Integration Settings` → `Throne API`（自 1.4.0 起） | 关闭；开启时端口为 `9095` | 仅限本机（`127.0.0.1`）；如果 `Listen on` 设为 `Local network`，则为所有能访问你电脑的设备，并受 `Allowed addresses` 限制。每个客户端都需要密钥。参见 [Throne API 与 thronectl](@/advanced/api.zh.md#security)。 |

在简洁模式下，上述入站设置中只显示 `监听端口`（Listen Port）。其他入站设置、`DNS 服务器端口`、`核心`（Core）标签页和 `Integration Settings` 仅在高级模式下显示（参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）。

当配置档使用 Xray 时，核心还会在 `127.0.0.1` 上打开若干内部端口，每个端口都由随机密码保护。Throne 与其核心之间通过本地套接字通信，而不是通过网络端口。

[无界面 Throne](@/headless/_index.zh.md) 在首次启动时就会开启 Throne API，监听 `127.0.0.1:9095`，密钥保存在一个只有其运行用户可以读取的文件中。

`thronectl` 的 Web 界面（`thronectl web`）是一个单独的程序，有自己的端口，默认为 `127.0.0.1:9096`，并且需要密码。参见 [Web 界面](@/headless/web.zh.md#security)。

**Android：** `设置`（Settings）→ `入站`（Inbound）中有同类选项：`代理端口`（Proxy port）、`允许来自局域网的连接`（Allow connections from the LAN）和 `启用授权`（Enable authorization），其中 `允许来自局域网的连接` 默认关闭。自 2.0.2 起，`设置` → `核心`（Core）中的 sing-box API 默认开启，监听 `127.0.0.1:9091` 并使用随机密钥；它用应用内的文件为侧边菜单中的 `sing-box 仪表板`（sing-box dashboard）提供服务。Throne for Android 没有 Throne API。

## 本网站 {#website}

本文档站点使用 Yandex Metrika 统计访问量。它会记录页面浏览量、来源页面、页面上和链接上的点击，以及访问者的停留时长。会话录制功能已关闭。Throne 应用中不包含此统计代码。

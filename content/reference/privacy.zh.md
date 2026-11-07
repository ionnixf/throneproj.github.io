+++
title = "隐私与网络请求"
description = "Throne 会向订阅服务商发送哪些信息、它自行发出的所有请求，以及谁可以使用它的本地端口。"
weight = 40
toc = true
+++

Throne 会按照你的配置档和路由规则发送你的流量。它自行发送的只有本页列出的内容：不发送使用统计，也不上传日志或崩溃转储。

## HWID 与设备信息 {#hwid}

如果你开启此功能（默认关闭），Throne 会在订阅请求中附带这些请求头，绝不会在其他请求中发送：

- `x-hwid`：Windows 的 `MachineGuid`、Mac 的硬件 UUID，或在 Linux 上为 `/etc/machine-id`（或 `/var/lib/dbus/machine-id`；两者都没有时，为 Throne 保存在配置文件夹 `hwid` 文件中的随机 ID）。
- `x-device-os`：`Windows`、`Linux` 或 `macOS`。
- `x-ver-os`：Windows 或 macOS 版本，或 Linux 内核版本。
- `x-device-model`：Windows 上为电脑型号（及主板），Linux 上为发行版，例如 `Ubuntu 24.04.1 LTS`，macOS 上为 macOS 名称和版本。

在 `设置`（Settings）→ `基本设置`（Basic Settings）→ `订阅`（Subscription）中开启 `启用在更新订阅时发送硬件ID、设备型号以及 OS 版本`（Enable sending HWID, device model, and OS version when updating subscription）（仅高级模式）。每个订阅请求还会发送 User-Agent `Throne/<version>`，除非你设置了其他值。要替换这些值或按分组设置，参见 [User-Agent 与 HWID](@/guides/subscriptions.zh.md#user-agent-and-hwid)。

**Android：** 相同的开关位于 `设置` → `订阅`（Subscriptions）。它发送 Android ID（或一个只生成一次的随机 ID）、`Android`、Android 版本和设备型号。默认 User-Agent 为 `Throne/Android/<version>`。

## 网络请求 {#network-requests}

标为“`使用代理`（Use proxy）规则”的请求直接发出，除非开启了 `使用代理`（`设置` → `基本设置` → `杂项`（Miscellaneous））或 `系统代理`（System Proxy）：此时它们经由本地代理端口发出，由你的路由配置档决定去向；没有配置档运行时，请求会失败。

| 请求 | 发出时机 | 目标 | 路由方式 |
| --- | --- | --- | --- |
| 订阅更新 | 添加或更新订阅时，手动或定时 | 你的服务商的 URL | `使用代理` 规则 |
| 远程路由配置档 | 添加或更新远程路由配置档时，手动或定时 | 该配置档的 URL；`raw.githubusercontent.com` 上的地址经由 `远程规则集镜像`（Remote Rule-set Mirror）访问 | `使用代理` 规则 |
| 路由配置档列表 | `路由`（Routing）→ `下载配置档`（Download Profiles） | GitHub 上的 throneproj/routeprofiles 仓库，经由镜像访问 | `使用代理` 规则 |
| 规则集 | 配置档需要尚未缓存的规则集时，以及配置档运行期间大约每天一次 | 经由 `远程规则集镜像`（默认为 `jsDelivr(Cloudflare)`）访问 GitHub，或你添加的 `.srs` URL | 经由路由配置档的默认出站（默认出站为 `block` 时直接发送） |
| 出口国家查询 | 每次启动配置档后（默认开启） | `Lookup service`；`Automatic` 依次尝试 `api.ip2location.io`、`ip-api.com`、`ipwho.is`、`ipinfo.io` 和 `www.cloudflare.com/cdn-cgi/trace` | 始终经由本地代理端口 |
| URL 测试 | 手动或自动的 URL 测试 | `延迟测试 URL`（Latency Test URL）（默认为 `http://cp.cloudflare.com/`） | 经由每个被测试的配置档 |
| IP 测试 | 出口 IP 测试 | `https://api.ip2location.io/` | 经由每个被测试的配置档 |
| 速度测试 | 速度测试 | Speedtest.net 服务器；`简单下载`（Simple Download）模式下为 `简单下载 URL`（Simple Download URL）（默认为 `http://cachefly.cachefly.net/1mb.test`） | 经由每个被测试的配置档 |
| 连通性检查 | 自动选择器运行期间，且仅当设置了 `直连测试 URL`（Direct Test URL）或 `连通性 URL`（Connectivity URL）时 | 该 URL | 直连 |
| 域名解析 | 解析你的服务器域名时 | 发往你的 `直连 DNS`（Direct DNS）服务器的 DNS 查询（默认为系统的解析器） | 直连 |
| 更新检查 | `检查更新`（Check For Update）；只有点击 `更新`（Update）后才会下载 | `api.github.com`（发布列表和更新日志），然后是 GitHub 上的发布文件 | `使用代理` 规则 |
| WARP 注册 | 生成 WARP 配置或身份时 | `api.cloudflareclient.com`，或你在 `注册域名...`（Registration Domains…）中设置的域名 | `使用代理` 规则 |
| Xray geo 文件 | 某个 Xray 配置需要 `geoip.dat` 或 `geosite.dat` 且你同意时，或点击 `下载`（Download）时 | `GeoIP 资源 URL`（GeoIP Asset URL）和 `GeoSite 资源的 URL`（GeoSite Asset URL）（默认指向 GitHub） | `使用代理` 规则 |
| IP 扫描 | 在 `IP Scanner` 中扫描时 | 先对公共 DNS 服务器（例如 `1.1.1.1:443`）进行 TCP 检查，然后是你扫描的地址 | 直连，TUN 模式下也是如此 |
| IP 列表更新 | 更新带有 URL 或规则集来源的 IP 列表时，手动或定时 | 该列表的 URL；内置列表来自 GitHub，经由镜像访问 | `使用代理` 规则 |
| 网络仪表盘 | `打开网络仪表盘`（Open Web dashboard），仅限没有内置仪表盘的版本（官方版本都内置了） | GitHub | 始终经由本地代理端口 |
| WebDAV 备份 | 上传、恢复、删除或测试时；从不自动进行 | 你的 `Server URL` 和 `Folder`：备份文件和备份列表 `throne_backups.json` | `使用代理` 规则；开启 `Connect through the proxy` 时始终经由本地代理端口 |
| NTP | 仅当开启 `NTP 设置`（NTP Settings）时 | 你输入的 NTP 服务器 | 你选择的 `出站`（outbound）（默认为 `direct`） |

出口国家查询会附带 Throne 的 User-Agent，对 ip-api.com 使用明文 HTTP，对其他服务使用 HTTPS。使用 `Default` 路由配置档时，查询服务看到的是你的代理地址，而不是你自己的地址。`设置` → `基本设置` → `通用`（Common）→ `Look up exit country` 可以关闭它（Android 上为 `设置` → `测试`（Testing））。

**WebDAV 备份**不会加密，包含你在 `创建备份`（Create Backup）下勾选的内容，例如服务器密码和订阅链接。备份列表会记录设备名称、一个随机的设备 ID、平台和 Throne 版本。`Username` 和 `Password` 通过 HTTP 基本认证（basic authentication）发送，使用 `http://` 时不加密，并且绝不会放入备份。参见 [WebDAV 备份](@/guides/backup.zh.md#webdav)。

更新检查和更新下载始终会验证 GitHub 的 TLS 证书，即使开启了 `忽略 TLS 错误`（Ignore TLS errors）也是如此。

来自 [Throne API](@/advanced/api.zh.md) 客户端的请求与上述请求相同，路由方式也相同。`thronectl` 的 Web 界面不会从其他网站加载任何内容。[无界面 Throne](@/headless/_index.zh.md) 不进行出口国家查询，并且会在不询问的情况下下载缺少的 Xray geo 文件。

你的应用流量所产生的 DNS 查询，会发往 `路由设置`（Routing Settings）→ `DNS` 中设置的服务器，或当前路由配置档的服务器，参见 [DNS](@/guides/dns.zh.md)。

**Android：**

- 开启 `设置` → `订阅` → `使用代理`（Use proxy）时，或处于 `仅代理`（Proxy only）模式时，只要有配置档在运行，它自身的请求就会经由本地代理。没有配置档运行时，如果开启了 `使用代理`，订阅更新和 WARP 注册会失败，否则直接发出。更新检查、路由配置档下载和规则集列表始终直接发出。
- `每天检查更新`（Check for updates daily，默认关闭）每天向 GitHub 检查一次更新。
- 当 Xray 配置需要 geo 文件时，它会在不询问的情况下从 GitHub 下载。
- WebDAV 备份从不经过本地代理。

## 本地代理端口 {#local-proxy}

配置档运行期间，Throne 会监听一个接受 SOCKS5 和 HTTP 连接的本地代理端口，默认为 `127.0.0.1:2080`。只有你自己电脑上的程序才能访问 `127.0.0.1`，但其中任何程序都无需密码即可使用该端口。在 `设置` → `基本设置` → `通用` → `入站设置`（Inbound Settings）中，将 `监听地址`（Listen Address）设为 `::` 或 `0.0.0.0` 会向其他设备开放该端口，`启用认证`（Enable Authorization，默认关闭）要求提供 `入站用户名`（Inbound Username）和 `入站密码`（Inbound Password），`禁用混合入站`（Disable Mixed Inbound）会关闭该端口。

`允许其他设备连接`（Allow other devices to connect）会把 `监听地址` 设为 `::`，此后任何能访问你电脑的设备都可以使用你的代理。请同时开启 `启用认证`，参见[局域网共享](@/guides/proxy_modes.zh.md#lan-sharing)。

| 其他本地端口 | 默认值 | 谁可以访问 |
| --- | --- | --- |
| `DNS 服务器端口`（DNS Server Port） | `5533` | 仅限本机，除非你勾选 `允许其他设备连接`（Allow other devices to connect），参见[供其他设备使用的 DNS 服务器](@/guides/dns.zh.md#dns-server)。 |
| Clash API | 关闭 | 它的 `监听地址`（默认为 `127.0.0.1`）。请设置 `密钥`（Secret）。 |
| sing-box API / 仪表盘 | 关闭 | 仅限本机，使用随机生成的 `密钥`。 |
| Throne API | 关闭；开启时端口为 `9095` | 仅限本机；如果 `Listen on` 设为 `Local network`，则为所有能访问它的设备，并受 `Allowed addresses` 限制。客户端需要密钥，参见 [Throne API 与 thronectl](@/advanced/api.zh.md#security)。 |

- 当配置档使用 Xray 时，核心还会在 `127.0.0.1` 上打开若干内部端口，每个端口都由随机密码保护。Throne 与其核心之间通过本地套接字通信，而不是通过网络端口。
- [无界面 Throne](@/headless/_index.zh.md) 在首次启动时就会开启 Throne API，监听 `127.0.0.1:9095`，密钥保存在一个只有其运行用户可以读取的文件中。
- `thronectl` 的 Web 界面有自己的端口，默认为 `127.0.0.1:9096`，并且需要密码，参见 [Web 界面](@/headless/web.zh.md#security)。
- **Android：** `设置` → `入站`（Inbound）中有 `代理端口`（Proxy port）、`允许来自局域网的连接`（Allow connections from the LAN，默认关闭）和 `启用授权`（Enable authorization）。sing-box API 默认开启，监听 `127.0.0.1:9091` 并使用随机密钥。没有 Throne API。

## 本网站 {#website}

本文档站点使用 Yandex Metrika 统计访问量：页面浏览量、来源页面、页面上和链接上的点击，以及访问者的停留时长。会话录制功能已关闭。Throne 应用中不包含此统计代码。

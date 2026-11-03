+++
title = "系统代理、TUN 与局域网共享"
description = "在系统代理和 TUN 模式之间做出选择，与网络中的其他设备共享 Throne，并设置本地代理端口。"
weight = 20
toc = true
+++

Throne 可以通过两种方式接管应用的流量：作为系统代理，或者使用 TUN 模式。本页介绍这两种方式，帮助你做出选择，并说明网络中的其他设备如何也能使用 Throne。

两种模式都只在配置档运行时生效。此时 Throne 还会监听一个本地代理端口，即混合端口。默认情况下它是 `127.0.0.1:2080`，同时接受 SOCKS 和 HTTP 代理连接。

当没有配置档运行，或者某个应用绕过了系统代理时，它的流量会使用你的普通连接。要改为拦截这类流量，请开启[断网保护（kill switch）](@/advanced/kill_switch.zh.md)（自 1.4.0 起）。

## 系统代理 {#system-proxy}

在主窗口勾选 `系统代理`（System Proxy），或在托盘菜单中使用 `操作模式`（Operation Mode）→ `系统代理`（System Proxy）。配置档运行时，Throne 会把操作系统的代理设置指向混合端口。当你停止配置档或退出 Throne 时，它会再次移除该设置。

- 只有遵循系统代理设置的应用才会使用它。大多数浏览器都会遵循。
- 忽略该设置的应用会直接连接，就像 Throne 没有运行一样。例如：Discord、Telegram 通话、终端和命令行工具、Microsoft Store（UWP）应用以及许多游戏。对这些应用请使用 [TUN 模式](#tun-mode)。
- 不需要管理员权限。

Throne 设置代理的方式取决于系统：

- **Windows：** Throne 写入 Windows 的代理设置。`代理格式`（Proxy Format，参见[入站设置](#inbound-settings)）决定它写入的文本：`{ip}:{port}`（默认）和 `http://{ip}:{port}` 表示 HTTP 代理，`socks={ip}:{port}` 表示 SOCKS 代理。
- **Linux：** 仅支持 GNOME 和 KDE。在其他桌面环境中，请在每个应用中分别设置代理。请以普通用户而不是 root 身份运行 Throne，否则它无法更改你桌面环境的代理设置。
- **macOS：** Throne 会为每个已启用的网络服务设置 HTTP、HTTPS 和 SOCKS 代理。

许多命令行工具会忽略系统代理，但会读取 `HTTP_PROXY` 和 `HTTPS_PROXY` 环境变量：

```bash
export HTTP_PROXY=http://127.0.0.1:2080 HTTPS_PROXY=http://127.0.0.1:2080
```

```powershell
$env:HTTP_PROXY = "http://127.0.0.1:2080"; $env:HTTPS_PROXY = "http://127.0.0.1:2080"
```

更多细节：

- 开启 `禁用混合入站`（Disable Mixed Inbound）时无法开启 `系统代理`（System Proxy）。此时 Throne 会显示“Cannot set system proxy when mixed inbound is disabled.”（混合入站已禁用时无法设置系统代理。）
- `设置`（Settings）→ `基本设置`（Basic Settings）→ `杂项`（Miscellaneous）→ `系统代理禁用时重启代理`（Restart Proxy On System Proxy Disable）（仅高级模式，参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）会在你关闭系统代理时重启正在运行的配置档，从而关闭已打开的连接。
- 勾选 `系统代理`（System Proxy）期间，Throne 也会通过混合端口发送它自己的请求，例如订阅更新。
- 如果 Throne 在配置档运行时被强制结束或崩溃，它就无法移除该设置，你的应用将无法连接。参见[常见问题](@/help/faq.zh.md#force-quit)。
- 要用按键切换系统代理，请设置全局热键 `切换系统代理`（Toggle System Proxy）。参见[键盘快捷键与托盘](@/reference/shortcuts.zh.md#global-hotkeys)。

## TUN 模式 {#tun-mode}

在主窗口勾选 `Tun 模式`（Tun Mode），或在托盘菜单中使用 `操作模式`（Operation Mode）→ `Tun 模式`（Tun Mode）。之后在配置档运行期间，Throne 会创建一个虚拟网卡，并让整台电脑的流量都经过它。

- 所有应用都会被覆盖，包括忽略代理设置的应用。
- DNS 查询也会经过该网卡，Throne 会按照它自己的 [DNS 设置](@/guides/dns.zh.md#how-dns-works)来应答。
- TUN 模式在 Windows 上需要管理员权限，在 Linux 和 macOS 上需要为核心授予 root 权限。Throne 会在第一次使用时请求这些权限。

有关设置、选项和故障排除，请参见 [TUN 模式](@/guides/tun_mode.zh.md)。

## 该用哪种模式 {#which-mode}

| | 系统代理 | TUN 模式 |
| --- | --- | --- |
| 覆盖的应用 | 仅限遵循系统代理设置的应用 | 所有应用 |
| 所需权限 | 无 | 管理员（Windows），为核心授予 root 权限（Linux、macOS） |
| DNS | 不接管。应用仍可以通过你平常的 DNS 服务器解析域名。 | 接管，并按 Throne 的 DNS 设置处理 |
| 针对应用的规则（`processName:`、`processPath:`） | 只对使用代理的应用生效 | 对所有应用生效 |
| 经过 Throne 的流量 | 仅限使用代理的应用的流量 | 所有流量，包括被规则设为直连的流量，但绕过的地址范围除外 |
| 常见问题 | 应用忽略代理 | 防火墙、杀毒软件或其他 VPN 拦截网卡 |

简而言之：浏览器使用系统代理就足够了。对于忽略系统代理的应用，以及通话和游戏，请使用 TUN 模式。针对单个应用的规则也只有在 TUN 模式下才能可靠生效，因为忽略系统代理的应用根本不会到达 Throne。另请参见[常用方案](@/guides/recipes.zh.md#calls-and-games)。

你可以同时开启两种模式。此时遵循系统代理的应用使用混合端口，其余所有流量由 TUN 模式接管。窗口标题会显示 `[Tun+系统代理]`（`[Tun+System Proxy]`）。

开启 `程序`（Program）→ `记住上次的配置档`（Remember last profile）后，Throne 启动时会启动上次的配置档；如果你退出时 `系统代理`（System Proxy）和 `Tun 模式`（Tun Mode）处于开启状态，还会重新开启它们。

自 1.4.0 起，`路由设置`（Routing Settings）中的 `劫持`（Hijack）标签页和 Windows 上的 `系统 DNS`（System DNS）选项已被移除。它们原本用于忽略系统代理的应用；对这些应用请改用 `Tun 模式`（Tun Mode）。

## 与其他设备共享代理 {#lan-sharing}

你网络中的其他设备，例如手机、电视、游戏机或其他电脑，可以把你电脑上的 Throne 用作它们的代理。

1. 选择 `程序`（Program）→ `允许其他设备连接`（Allow other devices to connect）。Throne 现在会监听所有网络接口，而不再只监听 `127.0.0.1`。
2. 如果有配置档正在运行，请点击“设置已更改，重启进行应用”通知中的 `重启`（Restart），或者停止并重新启动该配置档。
3. 在状态栏中找到你电脑的地址。状态栏现在会显示 `Mixed:`，后面跟着你的局域网地址和端口，例如 `Mixed: 192.168.1.20:2080`。
4. 在防火墙中允许该端口的传入连接（见下文）。
5. 在另一台设备上，将代理服务器设为该地址和端口，类型选择 SOCKS5 或 HTTP。

{% alert_warning() %}
如果不设密码，你网络中的任何人都可以使用你的代理。请在[入站设置](#inbound-settings)中开启 `启用认证`（Enable Authorization），并设置 `入站用户名`（Inbound Username）和 `入站密码`（Inbound Password）。然后在其他设备上输入相同的用户名和密码。
{% end %}

防火墙：

- **Windows：** 当核心首次接受来自网络的连接时，Windows 防火墙会询问是否允许 `ThroneCore`。请允许。如果你之前阻止了它，请在 `Windows 安全中心`（Windows Security）→ `防火墙和网络保护`（Firewall & network protection）→ `允许应用通过防火墙`（Allow an app through firewall）中允许 `ThroneCore`。
- **Linux：** 如果启用了防火墙，请开放该端口，例如使用 `sudo ufw allow 2080`。
- **macOS：** 如果 macOS 防火墙已开启，请在 macOS 询问时允许传入连接。

当 Throne 监听所有接口时，`连接`（Connections）标签页会显示 `来源`（Source）列，其中是每台设备的地址。要停止共享，请再次选择 `程序`（Program）→ `允许其他设备连接`（Allow other devices to connect），然后重启配置档。之后 Throne 只会监听 `127.0.0.1`。

`允许其他设备连接`（Allow other devices to connect）只共享代理端口。自 1.4.0 起，其他设备（例如路由器）也可以使用 Throne 的 DNS 服务器；参见[供其他设备使用的 DNS 服务器](@/guides/dns.zh.md#dns-server)。

共享代理端口与把你的整个网络连接作为 Wi-Fi 热点共享是两回事。关于后者以及更多示例，请参见[常用方案](@/guides/recipes.zh.md#share-with-devices)。

## 入站设置 {#inbound-settings}

这些设置控制混合端口。打开 `设置`（Settings）→ `基本设置`（Basic Settings）→ `通用`（Common），查看 `入站设置`（Inbound Settings）部分。在简洁模式下，该部分只显示 `监听端口`（Listen Port）；其他设置会被隐藏，但保留其值。要为[与其他设备共享代理](#lan-sharing)设置用户名和密码，请切换到高级模式（参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）。

| 设置 | 默认值 | 作用 |
| --- | --- | --- |
| `监听地址`（Listen Address） | `127.0.0.1` | 混合端口监听的地址。`127.0.0.1` 只接受本机上的应用。`::` 或 `0.0.0.0` 还接受其他设备；`允许其他设备连接` 会将其设为 `::`。 |
| `监听端口`（Listen Port） | `2080` | 混合端口。它接受 SOCKS（4、4a 和 5）和 HTTP 代理连接。 |
| `随机端口`（Random port） | 关闭 | 每次 Throne 启动时选择一个空闲端口。状态栏会显示当前端口。 |
| `启用认证`（Enable Authorization） | 关闭 | 所有到混合端口的连接都必须提供 `入站用户名` 和 `入站密码`。 |
| `禁用混合入站`（Disable Mixed Inbound） | 关闭 | 关闭混合端口。此时 `系统代理` 无法工作。`使用代理`（Use proxy）只有在 TUN 模式开启时才能工作（自 1.4.0 起；此前完全无法工作）。TUN 模式仍然可用。 |
| `自定义入站`（Custom Inbound） | 空 | `编辑`（Edit）会打开一个 JSON 编辑器，用于添加额外的 sing-box 入站：一个带有 `inbounds` 列表的对象。 |
| `代理格式`（Proxy Format） | `{ip}:{port}` | 仅限 Windows。Throne 写入 Windows 代理设置的文本。 |

如果更改这些设置时有配置档正在运行，请重启该配置档以应用更改。

## Android 版 {#android}

Throne for Android 则提供两种运行模式：`VPN`，其工作方式类似 TUN 模式，也是默认模式；以及 `仅代理`（Proxy only），它只开启一个本地代理端口。它还支持分应用代理和局域网访问。参见 [VPN 与代理模式](@/android/modes.zh.md)。

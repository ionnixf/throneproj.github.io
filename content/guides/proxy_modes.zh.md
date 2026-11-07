+++
title = "系统代理、TUN 与局域网共享"
description = "在系统代理和 TUN 模式之间做出选择，与网络中的其他设备共享 Throne，并设置本地代理端口。"
weight = 20
toc = true
+++

系统代理和 TUN 模式都只在配置档运行时生效。此时 Throne 还会监听混合端口（默认 `127.0.0.1:2080`），接受 SOCKS 和 HTTP 代理连接。

当没有配置档运行，或者某个应用绕过了系统代理时，它的流量会使用你的普通连接。要改为拦截这类流量，请开启[断网保护（kill switch）](@/advanced/kill_switch.zh.md)。

## 系统代理 {#system-proxy}

在主窗口勾选 `系统代理`（System Proxy），或在托盘菜单中使用 `操作模式`（Operation Mode）→ `系统代理`（System Proxy）。配置档运行时，Throne 会把系统的代理设置指向混合端口。不需要管理员权限。

只有遵循系统代理设置的应用才会使用它，例如大多数浏览器。其他应用会直接连接，例如 Discord、Telegram 通话、终端、Microsoft Store（UWP）应用以及许多游戏。

- **Windows：** Throne 按[入站设置](#inbound-settings)中的 `代理格式`（Proxy Format）写入 Windows 的代理设置。
- **Linux：** 仅支持 GNOME 和 KDE。请以普通用户而不是 root 身份运行 Throne，否则它无法更改该设置。
- **macOS：** Throne 会为每个已启用的网络服务设置 HTTP、HTTPS 和 SOCKS 代理。

许多命令行工具会忽略系统代理，但会读取 `HTTP_PROXY` 和 `HTTPS_PROXY`：

```bash
export HTTP_PROXY=http://127.0.0.1:2080 HTTPS_PROXY=http://127.0.0.1:2080
```

```powershell
$env:HTTP_PROXY = "http://127.0.0.1:2080"; $env:HTTPS_PROXY = "http://127.0.0.1:2080"
```

- 如果 Throne 在配置档运行时被强制结束或崩溃，该设置会保留，你的应用将无法连接。参见[常见问题](@/help/faq.zh.md#force-quit)。
- 勾选 `系统代理`（System Proxy）期间，Throne 自己的请求（例如订阅更新）也会通过混合端口发送。

## TUN 模式 {#tun-mode}

在主窗口勾选 `Tun 模式`（Tun Mode），或在托盘菜单中使用 `操作模式`（Operation Mode）→ `Tun 模式`（Tun Mode）。Throne 随后会让整台电脑的流量（包括 DNS）都经过一个虚拟网卡。这需要管理员或 root 权限；参见 [TUN 模式](@/guides/tun_mode.zh.md)。

## 该用哪种模式 {#which-mode}

| | 系统代理 | TUN 模式 |
| --- | --- | --- |
| 覆盖的应用 | 仅限遵循系统代理的应用 | 所有应用 |
| DNS | 应用仍可以使用你平常的 DNS 服务器 | 按 Throne 的 [DNS 设置](@/guides/dns.zh.md#how-dns-works)处理 |
| 针对应用的规则（`processName:`、`processPath:`） | 只对使用代理的应用生效 | 对所有应用生效 |
| 常见问题 | 应用忽略代理 | 防火墙、杀毒软件或其他 VPN 拦截网卡 |

浏览器使用系统代理就足够了。对于忽略系统代理的应用、通话和游戏，以及针对单个应用的规则，请使用 TUN 模式（参见[常用方案](@/guides/recipes.zh.md#calls-and-games)）。你也可以同时开启两种模式：遵循系统代理的应用使用混合端口，其余流量由 TUN 模式接管。

开启 `程序`（Program）→ `记住上次的配置档`（Remember last profile）后，Throne 启动时会启动上次的配置档；如果你退出时 `系统代理`（System Proxy）和 `Tun 模式`（Tun Mode）处于开启状态，还会重新开启它们。

## 与其他设备共享代理 {#lan-sharing}

你网络中的其他设备，例如手机、电视或游戏机，可以把你电脑上的 Throne 用作它们的代理。

1. 选择 `程序`（Program）→ `允许其他设备连接`（Allow other devices to connect），然后重启正在运行的配置档。
2. 在状态栏中查看你电脑的地址和端口，例如 `Mixed: 192.168.1.20:2080`。
3. 在防火墙中允许该端口的传入连接：在 Windows 上，当 Windows 防火墙询问时允许 `ThroneCore`（或之后在 `允许应用通过防火墙`（Allow an app through firewall）中允许）；在 Linux 上，例如 `sudo ufw allow 2080`；在 macOS 上，当 macOS 询问时允许。
4. 在另一台设备上，用该地址和端口设置 SOCKS5 或 HTTP 代理。

{% alert_warning() %}
如果不设密码，你网络中的任何人都可以使用你的代理。请在[入站设置](#inbound-settings)中开启 `启用认证`（Enable Authorization），设置 `入站用户名`（Inbound Username）和 `入站密码`（Inbound Password），并在其他设备上输入它们。
{% end %}

其他设备（例如路由器）也可以使用 Throne 的 [DNS 服务器](@/guides/dns.zh.md#dns-server)。要把整个网络连接作为 Wi-Fi 热点共享，请参见[常用方案](@/guides/recipes.zh.md#hotspot)。

## 入站设置 {#inbound-settings}

这些设置控制混合端口：`设置`（Settings）→ `基本设置`（Basic Settings）→ `通用`（Common）→ `入站设置`（Inbound Settings）。在简洁模式下只显示 `监听端口`（Listen Port）（参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）。如果有配置档正在运行，请重启它以应用更改。

| 设置 | 默认值 | 作用 |
| --- | --- | --- |
| `监听地址`（Listen Address） | `127.0.0.1` | `127.0.0.1` 只接受本机上的应用；`::` 或 `0.0.0.0` 还接受其他设备。`允许其他设备连接` 会将其设为 `::`。 |
| `监听端口`（Listen Port） | `2080` | 混合端口，用于 SOCKS（4、4a 和 5）和 HTTP。 |
| `随机端口`（Random port） | 关闭 | 每次启动时选择一个空闲端口，显示在状态栏中。 |
| `启用认证`（Enable Authorization） | 关闭 | 要求提供 `入站用户名` 和 `入站密码`。 |
| `禁用混合入站`（Disable Mixed Inbound） | 关闭 | 关闭混合端口。此时无法开启 `系统代理`，Throne 自身请求的 `使用代理`（Use proxy）只在 TUN 模式下有效。 |
| `自定义入站`（Custom Inbound） | 空 | 额外的 sing-box 入站：一个带有 `inbounds` 列表的 JSON 对象。 |
| `代理格式`（Proxy Format） | `{ip}:{port}` | 仅限 Windows。`{ip}:{port}` 或 `http://{ip}:{port}` 表示 HTTP 代理，`socks={ip}:{port}` 表示 SOCKS 代理。 |

## Android 版 {#android}

Throne for Android 提供 `VPN` 模式（类似 TUN 模式，也是默认模式）和 `仅代理`（Proxy only）模式。参见 [VPN 与代理模式](@/android/modes.zh.md)。

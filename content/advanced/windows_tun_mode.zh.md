+++
title = "Windows DNS 泄漏防护"
description = "在 Windows 上，严格路由如何阻止 TUN 模式下的 DNS 泄漏，以及无法使用它时该怎么办。"
weight = 60
toc = true
aliases = ["/zh/advanced/windows-tun-mode/"]
+++

在 [TUN 模式](@/guides/tun_mode.zh.md)下，Windows 仍可能通过你平常的网卡发送 DNS 查询，这样你的运营商就能看到你查询了哪些网站。（在 `系统代理`（System Proxy）模式下，`严格路由`（Strict Route）不起作用。）

## 严格路由 {#strict-route}

Windows 会把每个 DNS 查询同时发给所有网卡，因此查询也会从隧道之外发出。在 TUN 模式运行期间，`严格路由` 会添加 Windows 防火墙过滤规则，用于：

- 在除隧道以外的所有网卡上阻止 DNS（端口 53）；
- 在 `Tun 启用 IPv6`（Tun Enable IPv6）关闭时阻止其他应用的 IPv6 连接。

这些过滤规则也可能阻止需要在隧道之外使用 DNS 或 IPv6 的程序。

`严格路由` 在 Windows 10 和 11 上默认开启。要确认，请打开 `设置`（Settings）→ `Tun 设置`（Tun Settings），确认已勾选 `严格路由`，点击 `确定`（OK），然后将 `Tun 模式`（Tun Mode）关闭后再重新开启。

### 未开启的情况 {#when-off}

- **Windows 7、8 或 8.1：** `严格路由` 在这些系统上无法工作。
- **“严格的路由不可用”（Strict routing unavailable）：** Windows 无法设置过滤规则，因此配置档没有启动。按照消息的建议关闭 `严格路由` 会失去防护。排除故障原因后，请重新开启它。
- **最初安装的是 Throne 1.2.0：** 该版本默认关闭此项，而更新会保留已保存的设置。请勾选它。

## 注册表备用方案 {#registry-fallback}

不使用 `严格路由` 时，可以关闭 Windows 中向所有网卡发送 DNS 查询的功能（组策略 `关闭智能多宿主名称解析`（Turn off smart multi-homed name resolution））。这种方法较弱：它只改变 Windows 询问哪些 DNS 服务器，并不阻止任何流量。它需要 Windows 8 或更高版本。

### 使用组策略（专业版、企业版、教育版） {#group-policy}

1. 从开始菜单打开 `编辑组策略`（Edit group policy）。
2. 依次进入 `计算机配置`（Computer Configuration）→ `管理模板`（Administrative Templates）→ `网络`（Network）→ `DNS 客户端`（DNS Client）。
3. 双击 `关闭智能多宿主名称解析`，选择 `已启用`（Enabled），然后点击 `确定`（OK）。
4. 重启 Windows。

{% alert_warning() %}
请选择 `已启用`，而不是 `已禁用`（Disabled）：启用此策略才会关闭该功能。
{% end %}

要撤销此设置，请将该策略设回 `未配置`（Not Configured）。

### 使用注册表（Windows 家庭版） {#windows-home}

在开始菜单中右键点击 `命令提示符`（Command Prompt），选择 `以管理员身份运行`（Run as administrator），运行以下命令，然后重启 Windows：

```text
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\DNSClient" /v DisableSmartNameResolution /t REG_DWORD /d 1 /f
```

要撤销此设置，请以同样的方式运行以下命令并重启 Windows：

```text
reg delete "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\DNSClient" /v DisableSmartNameResolution /f
```

## 浏览器 {#browsers}

### 加密 DNS {#encrypted-dns}

浏览器中的 DNS over HTTPS，或 Windows 11 为网卡启用的 DNS over HTTPS，都不使用端口 53。`严格路由` 不会阻止它，它也会绕过 Throne 的 DNS，因此泄漏测试会显示该 DNS 服务商。请在浏览器的隐私或安全设置中关闭安全 DNS，并在 Windows 设置中为网卡关闭 DNS over HTTPS。

### QUIC {#quic}

QUIC 不会导致 DNS 泄漏。但如果 Google、YouTube 等使用 QUIC（基于 UDP 的 HTTP/3）的网站加载缓慢或无法打开，而其他网站正常，可能是你的代理对 UDP 的支持很差。在 Chrome 中，打开 `chrome://flags/`，将 `Experimental QUIC protocol` 设为 `Disabled`，然后点击 `Relaunch`。

## 仍有泄漏？ {#still-leaking}

- **检查路由设置。** 被规则设为直连的域名会有意使用 `直连 DNS`（Direct DNS）解析。预期结果请参见 [DNS 泄漏测试](@/guides/dns.zh.md#leak-tests)。
- **暂停安全软件。** 有些杀毒软件和防火墙产品会过滤或重定向 DNS。请在不使用它们的情况下测试，或者为 Throne 添加例外。
- **断开其他 VPN 应用。** 它们也会修改 DNS 设置和防火墙规则。
- **使用[断网保护（kill switch）](@/advanced/kill_switch.zh.md)。** 它会拦截所有绕过 Throne 的流量，包括发往本地网络的 DNS，即使没有配置档运行时也是如此。

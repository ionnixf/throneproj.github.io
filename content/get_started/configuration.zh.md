+++
title = "快速入门"
description = "在 Windows、Linux 或 macOS 上使用 Throne 添加服务器、测试服务器、选择路由配置档并连接。"
weight = 2
toc = true
+++

你需要服务商提供的订阅链接或分享链接，或者你自己服务器的详细信息。本页的所有步骤都可以在简洁模式下完成，全新安装默认就是简洁模式。Android 版请参阅 [Throne for Android](@/android/_index.zh.md#quick-start)。

## 添加服务器 {#add-servers}

Throne 将每个服务器保存为一个**配置档**。配置档属于**分组**，分组就是配置档列表上方的标签页。**订阅**是一种分组，Throne 从某个 URL 下载它，之后还可以更新。

### 添加订阅 {#paste-subscription}

1. 复制订阅 URL。它以 `https://` 开头。
2. 在主窗口中按 `Ctrl+V`（或点击 `程序`（Program）→ `添加剪贴板中的配置档`（Add profile from clipboard））。
3. 选择 `创建新的订阅分组`（Create new subscription group），然后点击 `确定`（OK）。

Throne 会创建一个以 URL 中的主机名命名的分组，并下载其中的配置档。如果服务商发送了你的套餐信息，配置档列表上方会出现一张卡片，显示已用流量和到期日期。`添加配置档到这个分组`（Add profiles to this group）只会一次性导入配置档，不保存 URL，因此之后无法更新。

之后，你可以通过 `分组`（Groups）→ `更新订阅`（Update subscription）（`Ctrl+U`）更新当前分组。自动更新请参阅[订阅与分组](@/guides/subscriptions.zh.md#auto-update)。

### 添加单个配置档 {#single-profiles}

配置档会进入当前选中的标签页所对应的分组。

- **分享链接**，例如 `vless://`、`ss://` 或 `trojan://`：复制一条或多条链接（每行一条），然后按 `Ctrl+V`。
- **屏幕上的二维码**：`程序` → `扫描二维码`（Scan QR Code，`Ctrl+Shift+Q`）。
- **文件和二维码图片**：将它们拖放到主窗口，或点击 `程序` → `添加文件中的配置档`（Add profile from File(s)，`Ctrl+O`）。
- **手动添加**：`程序` → `新建配置档`（New profile，`Ctrl+N`）。

所有支持的链接和文件都列在[协议与导入格式](@/reference/protocols.zh.md#import-formats)中。

## 测试服务器 {#test-servers}

点击 `分组` → `URL 测试本组`（Url Test Group，`Ctrl+Shift+G`）即可测量分组中所有配置档的延迟。如果只想测试部分配置档，请选中它们，右键点击并选择 `URL 测试选定项`（Url Test Selected，`Ctrl+Shift+S`）。

`测试结果`（Test Result）列以毫秒为单位显示延迟，或显示 `不可用`（Unavailable）。点击列标题可以排序。`分组` → `清理`（Clean up）→ `移除不可用项`（Remove Unavailable，`Ctrl+Shift+R`）会删除测试失败的配置档；如果服务商仍然提供它们，订阅更新时会重新添加。

速度测试和自动选择服务器请参阅[测试与自动选择器](@/guides/testing.zh.md)。

## 选择路由 {#choose-routing}

这一步是可选的：内置路由配置档 `Default` 会让所有流量都经过代理。如果你希望本国网站直接打开：

1. 点击 `路由`（Routing）→ `下载配置档`（Download Profiles），然后选择 `China`、`Iran` 或 `Russia`。保持勾选 `自动更新`（Auto update），然后点击 `确定`。
2. 再次打开 `路由` 菜单，在底部选择 `Bypass China`、`Bypass Iran` 或 `Bypass Russia`。正在运行的配置档会使用新的路由重新启动。

“Bypass”配置档会让除该国网站和 IP 地址以及你的本地网络之外的所有流量经过代理。其他下载的配置档和自定义规则请参阅[路由](@/guides/routing.zh.md#download-profiles)。

## 连接 {#connect}

1. 点击你想使用的配置档。
2. 按 `Enter`，或点击 `工具`（Tools）右侧的启动按钮。
3. 勾选启动按钮旁边的 `系统代理`（System Proxy）或 `Tun 模式`（Tun Mode）。

| 模式 | 作用 |
|---|---|
| `系统代理` | 将系统的代理设置指向 Throne（默认为 `127.0.0.1:2080`）。浏览器和其他遵循这些设置的应用会使用代理。 |
| `Tun 模式` | 捕获所有应用的流量，包括忽略代理设置的应用。需要管理员或 root 权限。 |

你可以同时开启两者。如果两者都不勾选，你自行设置的应用仍然可以通过 `127.0.0.1:2080` 将 Throne 用作 SOCKS5 或 HTTP 代理。参见[该用哪种模式](@/guides/proxy_modes.zh.md#which-mode)。

第一次勾选 `Tun 模式` 时，Throne 会请求所需的权限。点击 `是`（Yes），然后：

- **Windows：** 确认 Windows 的提示。Throne 会以管理员身份重新启动，并开启 `Tun 模式`。如果 Windows 防火墙询问 `ThroneCore`，请允许。
- **Linux：** 输入你的密码，然后再次勾选 `Tun 模式`。
- **macOS：** 在打开的“终端”（Terminal）窗口中输入你的密码，然后再次勾选 `Tun 模式`。

详细信息请参阅 [TUN 权限](@/guides/tun_mode.zh.md#privileges)。

### 检查连接 {#check-connection}

窗口左下角会显示正在运行的配置档，片刻之后还会显示你的出口 IP 地址所在的国家和城市。点击它可以测试连接：`测试结果: <n> ms`（`Test Result: <n> ms`）表示连接正常。如果显示 `不可用`（Unavailable），请尝试其他配置档，或参阅[故障排除](@/help/troubleshooting.zh.md)。

### 停止和退出 {#stop-and-quit}

- 要断开连接，请再次点击启动按钮（此时为 `停止`（Stop）），或者按 `Ctrl+S`。
- 关闭窗口只会将 Throne 隐藏到系统托盘。要退出，请使用 `程序` → `退出`（Exit），或托盘菜单中的 `退出`。
- `程序` → `记住上次的配置档`（Remember last profile）会在 Throne 启动时重新连接，`程序` → `随系统启动`（Start with system）会在你登录时启动 Throne。

{% alert_warning() %}
请始终使用 `退出` 来关闭 Throne。如果在 `系统代理` 开启时强行结束 Throne，系统会继续使用一个已不再运行的代理，网站将无法加载。参见[常见问题](@/help/faq.zh.md#force-quit)。
{% end %}

## 简洁模式与高级模式 {#interface-mode}

**简洁**模式只显示大多数人需要的选项，并隐藏高级的菜单项、设置和标签页，例如 `预设设置`（Preset Settings）、`基本设置`（Basic Settings）中的 `日志`（Logging）和 `核心`（Core）标签页、大部分 DNS 选项，以及配置档编辑器中的高级选项。**高级**模式显示所有内容。全新安装会以简洁模式启动；更新后的安装会保留高级模式。

被隐藏的设置会保留原值并继续生效，因此切换模式不会改变其他任何内容。要切换模式，请打开 `设置`（Settings）→ `基本设置`（Basic Settings）→ `样式`（Style），将 `界面`（Interface）设为 `简洁`（Simple）或 `高级`（Advanced），然后点击 `确定`（OK）。

要查找任意设置，请按 `Ctrl+K` 并输入其名称的一部分。在简洁模式下，这项搜索也会找到被隐藏的设置；选择其中一项后，Throne 会切换到高级模式并打开该设置。参见[命令面板](@/reference/shortcuts.zh.md#command-palette)。

在简洁模式下，本文档提到的某些标签页会成为另一个标签页中的区域。例如，`基本设置` 中的 `订阅`（Subscription）和 `安全`（Security）标签页，以及 `路由设置`（Routing Settings）中的 `Warp` 标签页，都会成为各自窗口中 `通用`（Common）标签页的区域。

## 后续步骤 {#next-steps}

- [订阅与分组](@/guides/subscriptions.zh.md)：自动更新，以及部分服务商要求的 User-Agent 和 HWID 设置。
- [路由](@/guides/routing.zh.md)：你自己的规则，例如只让少数网站经过代理。
- [测试与自动选择器](@/guides/testing.zh.md)：速度测试，以及自动切换到可用的服务器。
- [TUN 模式](@/guides/tun_mode.zh.md)和 [DNS](@/guides/dns.zh.md)：设置、副作用和泄漏测试。
- [故障排除](@/help/troubleshooting.zh.md)和[常见问题](@/help/faq.zh.md)：遇到问题时的帮助。

+++
title = "备份、更新与迁移"
description = "备份和恢复 Throne，在桌面版与 Android 之间迁移数据，以及更新桌面版。"
weight = 70
toc = true
+++

每次更新前都请先备份。

## 备份与恢复 {#backup-restore}

打开 `设置`（Settings）→ `基本设置`（Basic Settings）→ `备份和恢复`（Backup and Restore）。要创建备份，请在 `创建备份`（Create Backup）下勾选需要的部分（默认全部勾选），点击 `创建备份...`（Create Backup...），然后选择 `.thrbackup` 文件的保存位置。

| 部分 | 包含内容 |
| --- | --- |
| `配置档(分组和代理)`（Profiles (groups and proxies)） | 分组、订阅和配置档。 |
| `路由配置档`（Routing profiles） | 路由配置档及其规则。 |
| `设置`（Settings） | 所有设置，包括 DNS、TUN、WARP 和入站设置。 |
| `OTP 配置档`（OTP profiles） | [OTP 管理器](@/advanced/vpn_profiles.zh.md#otp-manager)中的条目。 |
| `IP lists and scans` | [IP 扫描器](@/guides/testing.zh.md#ip-scanner)的列表及结果。 |
| `自定义图标`（Custom icons） | 自定义托盘图标。 |

备份不包含流量历史、日志、核心的缓存，也不记录 `Tun 模式`（Tun Mode）或 `系统代理`（System Proxy）是否开启。

{% alert_warning() %}
备份中包含你的服务器密码、订阅链接和其他机密信息。请妥善保管该文件，切勿将其附在 Bug 报告中。
{% end %}

恢复备份：

1. 点击 `从备份恢复...`（Restore from Backup...），然后选择一个 `.thrbackup` 文件。Throne 会显示它的创建时间和创建它的设备。
2. 勾选要恢复的部分并点击 `恢复`（Restore）。每个部分都会替换你当前的数据，且无法撤销。
3. Throne 会重启。

- 旧版本创建的备份可以正常恢复。较新版本创建的备份可能会被拒绝，并提示“不支持的备份格式版本”（Unsupported backup format version）：请先更新 Throne。
- 使用了你的版本不支持的条件的路由规则会被跳过，Throne 会告诉你跳过了多少条。
- 如果只恢复 `配置档(分组和代理)` 和 `路由配置档` 中的一个部分，拥有自己路由配置档的分组会改回使用默认路由配置档。

无界面（headless）运行的 Throne 请参见 [thronectl](@/headless/thronectl.zh.md#from-desktop)。

## WebDAV 备份 {#webdav}

Throne 桌面版和 Throne for Android 都可以把备份保存在 WebDAV 服务器上，例如 Nextcloud、NAS 或 Yandex Disk，并且每个应用都能恢复另一个应用上传的备份。在桌面版中，通过 `备份和恢复` 中的 `WebDAV Settings...` 设置服务器，然后点击 `Upload to WebDAV`，上传 `创建备份` 下勾选的部分。Throne 不会定时上传，也不会自动删除旧备份。

{% alert_warning() %}
备份不会加密。任何能读取该 WebDAV 文件夹的人都能读取你的服务器密码和订阅链接。请使用你信任的服务器，并通过 `https://` 连接。
{% end %}

### WebDAV 设置 {#webdav-settings}

| 字段 | 作用 |
| --- | --- |
| `Server URL` | WebDAV 地址，以 `http://` 或 `https://` 开头，例如 Nextcloud 的 `https://cloud.example.com/remote.php/dav/files/me/`。 |
| `Username`、`Password` | 你的登录信息。它们通过 HTTP 基本认证（basic authentication）发送，因此在 `http://` 下不会加密。 |
| `Folder` | 服务器地址下的文件夹（默认 `Throne`，子文件夹用 `/` 分隔）。缺少的文件夹由 Throne 创建。 |
| `Device name` | 备份列表中这台电脑显示的名称（默认为电脑的名称）。 |
| `Connect through the proxy` | 通过 Throne 的本地代理发送请求。需要有正在运行的配置档。 |

`Test` 可以在你用 `Save` 保存之前测试这些值。这些设置只属于这台电脑：备份永远不会包含它们。

不勾选 `Connect through the proxy` 时，WebDAV 请求会直接发出，除非开启了 `使用代理`（Use proxy）或 `系统代理`（参见[网络请求](@/reference/privacy.zh.md#network-requests)）。Throne 会检查服务器的 TLS 证书，除非开启了 `忽略 TLS 错误`（Ignore TLS errors）。

### 恢复或删除备份 {#webdav-restore}

1. 点击 `Restore from WebDAV...`。Throne 会列出备份，最新的在最上面，并显示设备、平台、版本和所含部分。
2. 选中一个备份并点击 `恢复`（Restore）（或双击它）。
3. 从[备份与恢复](#backup-restore)的第 2 步继续。

Throne 会根据备份列表检查每个下载的文件。不在列表中的文件，例如你自己复制进去的文件，会显示为“Unknown device”（未知设备），无法检查。要删除备份，请选中它并点击 `Delete`。

| 提示 | 处理方法 |
| --- | --- |
| “Wrong username or password.” | 检查 `Username` 和 `Password`。启用了两步验证的服务通常需要应用专用密码。 |
| “Permission denied.” | 该账号无权写入此 `Folder`。请选择其他文件夹，或检查服务器上的权限。 |
| “The server redirected to …; check the server URL.” | 把提示中的地址填入 `Server URL`。 |
| “The server's folder listing could not be read.” | `Server URL` 不是 WebDAV 地址，例如是你的存储的网页。 |
| “The server did not answer in time.” | 请检查连接，或尝试 `Connect through the proxy`。 |
| “有用代理的请求，但未启动配置档。”（Request with proxy but no profile started.） | 启动一个配置档，或关闭 `Connect through the proxy`、`使用代理` 和 `系统代理`。 |

### Android 上的 WebDAV {#webdav-android}

在 Throne for Android 中，打开 `设置`（Settings）→ `备份与恢复`（Backup & restore）（或侧边菜单中的 `工具`（Tools））。`WebDAV`（WebDAV backup）卡片中有 `备份到 WebDAV`（Back up to WebDAV）、`从 WebDAV 恢复`（Restore from WebDAV）和 `WebDAV 设置`（WebDAV settings）。要与桌面版共享备份，请填写相同的服务器和文件夹：桌面版的 `Folder` 对应这里的 `备份目录`（Backup path）。`备份到 WebDAV` 会上传 `本地`（Local backup）卡片中勾选的部分。

Android 没有用于 WebDAV 的代理选项：在 VPN 模式下，这些请求会像其他应用的流量一样经过 VPN。它也从不忽略 TLS 证书错误。

## 在桌面版与 Android 之间迁移 {#desktop-android}

桌面版和 Throne for Android 使用相同的备份格式，因此你可以通过文件或 [WebDAV](#webdav) 双向迁移数据。

从桌面版迁移到手机：

1. 在桌面版中创建备份，并将 `.thrbackup` 文件复制到手机上。
2. 在手机上打开 `设置`（Settings）→ `备份与恢复`（Backup & restore），点按 `恢复`（Restore）并选择该文件（或从文件管理器中打开它）。
3. 勾选要恢复的部分并确认。

从手机迁移到桌面版：在手机上打开 `设置` → `备份与恢复`，勾选要备份的部分，然后点按 `创建备份`（Create backup）（`分享`（Share）可将文件发送到其他应用）。将文件复制到电脑上，然后[恢复它](#backup-restore)。

在 Android 上恢复桌面版备份时，有几处会发生变化：

| 项目 | 在 Android 上 |
| --- | --- |
| `OTP 配置档`、`IP lists and scans`、`自定义图标`，以及分组的 `Server Address` | 不会恢复。 |
| 两个应用都有的设置 | 采用备份中的值。例如，TUN MTU 会变为 1500（Android 默认值：9000）。 |
| `允许来自局域网的连接`（Allow connections from the LAN） | 如果备份允许其他设备连接而你的手机原先不允许，会被关闭并显示警告。 |
| 使用 Android 不支持的条件的路由规则 | 会被跳过并显示警告。 |
| 原始路由配置档 | 会保留，但为只读且不会使用。如果其中某个原本处于活动状态，第一个可用的路由配置档会成为活动配置档。 |
| 路由配置档中的 OpenVPN 和 OpenConnect 端点 | 会保留，但不会运行。 |
| Tailscale 和额外核心（Extra Core）配置档 | 会恢复，但无法启动。 |

反过来，匹配 Android 应用的规则以及分应用代理设置在桌面版上不起作用。

## 更新桌面版 {#updating}

更新会保留你的配置档和设置（它们存放在[数据文件夹](@/reference/files.zh.md#data-folder)中），但仍请先创建备份。

1. 打开 `程序`（Program）→ `检查更新`（Check For Update）（1.4.0 之前为 `工具`（Tools）→ `检查更新`）。
2. 如果有更新的版本，`更新`（Update）窗口会显示更改内容。点击 `更新`（Update）。
3. Throne 询问是否重启时，点击 `是`（Yes）。更新程序会安装新版本。

一键 `更新` 仅适用于 ZIP 版本以及 Windows 安装程序为当前用户执行的默认安装，并且在同一安装目录中运行着后台 Throne 时不可用。其他安装方式的更新方法如下：

| 安装方式 | 更新方法 |
| --- | --- |
| ZIP 文件 | 使用 `更新`，或用新的 ZIP 替换程序文件。保留 `config` 文件夹。 |
| Windows 安装程序，当前用户（默认） | 使用 `更新`，或运行新的安装程序。 |
| Windows 安装程序，所有用户 | 运行新的安装程序。 |
| `.deb` 或 `.rpm` 软件包 | 安装新的软件包。 |
| Linux 安装脚本 | 再次运行该脚本。 |
| macOS | 用新版本替换 `Throne.app`。参见[安装](@/get_started/installation.zh.md#macos)。 |
| WinGet、Scoop、AUR、Nix、Homebrew，或 APT、RPM 仓库 | 使用包管理器更新。参见[包管理器](@/get_started/installation.zh.md#package-managers)。 |

如果你用 Windows 安装程序重新安装，当卸载程序询问是否同时删除配置档、设置和日志时，请选择 `否`（No）。

由于更新程序会替换程序文件，一些杀毒软件会将其标记为可疑。如果更新失败，请确认 Throne 不在你的“下载”（Downloads）文件夹中（[#1109](https://github.com/throneproj/Throne/issues/1109)），然后重试；或者从[下载页面](@/downloads.zh.md)下载新版本。

## Beta 版 {#beta}

Beta 版（预发布版本）会率先获得功能和修复，但可能存在更多错误。安装 Beta 版之前请先备份。

- **桌面版：** 勾选 `设置`（Settings）→ `基本设置`（Basic Settings）→ `杂项`（Miscellaneous）→ `允许更新到 beta 版`（Allow updating to beta versions）（在简洁模式下，位于 `通用`（Common）选项卡的 `网络设置`（Network Settings）中）。
- **Android：** 开启 `设置`（Settings）→ `通用`（General）→ `允许更新到 Beta 版本`（Allow updating to beta versions）。参见[应用内更新程序](@/android/installation.zh.md#updater)。

如需退回，请关闭该选项。更新程序只提供更新的版本，因此在更新的稳定版发布之前，你会一直停留在 Beta 版上。

## 旧版本 {#old-versions}

- **早于 1.1.0 的 Throne 桌面版：** 1.1.0 将所有数据移入了 `throne.db`，不会转换旧的配置。请重新添加订阅和配置档，或者试试 [#1202](https://github.com/throneproj/Throne/issues/1202) 中的社区脚本。1.1.3 及更高版本创建的备份可以在更新的版本中恢复。
- **Nekoray 或 NekoBox：** 参见[从 Nekoray / NekoBox 迁移](@/help/migrating.zh.md)。
- **早于 2.0.0 的 Throne for Android：** 2.0.0 使用新的密钥签名，因此请先卸载旧应用。2.0.0 以空白状态启动，无法恢复旧备份（`throne_backup_*.json` 或 WebDAV `.zip` 文件），只能恢复 `.thrbackup` 文件。卸载前请记下你的订阅链接，或改用桌面版备份迁移全部数据。参见[安装与升级](@/android/installation.zh.md#upgrading)。

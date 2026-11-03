+++
title = "备份、更新与迁移"
description = "备份和恢复 Throne，在桌面版与 Android 之间迁移数据，更新桌面版，以及从旧版本升级。"
weight = 70
toc = true
+++

本页介绍如何将你的配置档和设置保存到文件或 WebDAV 服务器、如何恢复它们，以及如何在 Throne 桌面版与 Throne for Android 之间迁移它们。本页还说明如何更新桌面版，以及从旧版本升级时需要做什么。每次更新前都请先备份。

## 备份与恢复 {#backup-restore}

打开 `设置`（Settings）→ `基本设置`（Basic Settings）→ `备份和恢复`（Backup and Restore）。

创建备份：

1. 在 `创建备份`（Create Backup）下，勾选你需要的部分。默认勾选所有部分。
2. 点击 `创建备份...`（Create Backup...），然后选择文件的保存位置。默认建议保存为主文件夹中的 `Throne-backup.thrbackup`。
3. Throne 会确认已创建的文件，并列出其中包含的部分。

| 部分 | 包含内容 |
| --- | --- |
| `配置档(分组和代理)`（Profiles (groups and proxies)） | 你的分组、订阅和配置档。 |
| `路由配置档`（Routing profiles） | 你的路由配置档及其规则。 |
| `设置`（Settings） | 所有设置，包括 DNS、TUN、WARP 和入站设置。 |
| `OTP 配置档`（OTP profiles） | OTP 管理器中的条目。 |
| `IP lists and scans` | 你的 IP 列表和 [IP 扫描器](@/guides/testing.zh.md#ip-scanner)的扫描及其结果（自 1.4.0 起）。 |
| `自定义图标`（Custom icons） | 你的自定义托盘图标。 |

备份不包含流量历史、日志或核心的缓存文件。它也不会记录 `Tun 模式`（Tun Mode）或 `系统代理`（System Proxy）是否处于开启状态。自 1.4.0 起，备份还会记录 Throne 的版本以及创建它的设备名称：即电脑的名称，或 [WebDAV 设置](#webdav-settings)中的 `Device name`。

{% alert_warning() %}
备份中包含你的服务器密码、订阅链接和其他机密信息。请妥善保管该文件，切勿将其附在 Bug 报告中。
{% end %}

恢复备份：

1. 点击 `从备份恢复...`（Restore from Backup...），然后选择一个 `.thrbackup` 文件。
2. Throne 会显示备份的创建时间；如果文件中有记录，还会显示创建它的设备（“Created by: …”）。勾选你想要恢复的部分。文件中不包含的部分会显示为灰色。
3. 点击 `恢复`（Restore）。每个选中的部分都会替换你当前的数据。此操作无法撤销。
4. Throne 会重启。

旧版本 Throne 创建的备份可以正常恢复。较新版本创建的备份可能会被拒绝，并提示“不支持的备份格式版本”；请先更新 Throne。自 1.3.2 起，如果备份中的路由规则使用了此版本 Throne 不支持的条件（例如来自较新版本的规则），Throne 会跳过这些规则，并告诉你跳过了多少条。

自 1.4.0 起，如果只恢复 `配置档(分组和代理)`（Profiles (groups and proxies)）和 `路由配置档`（Routing profiles）中的一个部分，拥有自己路由配置档的分组会改回使用默认路由配置档。Throne for Android 自 2.0.5 起也是如此。

要备份或恢复无界面（headless）运行的 Throne，或把桌面版的备份发送给它，请参见 [thronectl](@/headless/thronectl.zh.md#from-desktop)。

## WebDAV 备份 {#webdav}

自 1.4.0 起，Throne 桌面版可以把备份保存在 WebDAV 服务器上，例如 Nextcloud、NAS 或 Yandex Disk。Throne for Android 也可以这样做。两个应用使用相同的文件夹结构和同一份备份列表，因此每个应用都可以恢复另一个应用上传的备份。参见 [Android 上的 WebDAV](#webdav-android)。

`设置`（Settings）→ `基本设置`（Basic Settings）→ `备份和恢复`（Backup and Restore）中的 `WebDAV` 分组有三个按钮：

| 按钮 | 作用 |
| --- | --- |
| `WebDAV Settings...` | 设置服务器、文件夹和这台电脑的名称。 |
| `Upload to WebDAV` | 用 `创建备份`（Create Backup）下勾选的部分创建备份并上传。 |
| `Restore from WebDAV...` | 列出服务器上的备份，供你恢复或删除其中一个。 |

只有在你点击 `Upload to WebDAV` 时，Throne 才会上传。没有自动上传或定时上传。

{% alert_warning() %}
备份不会加密。任何能读取该 WebDAV 文件夹的人，都能读取你的服务器密码和订阅链接。请使用你信任的服务器，并通过 `https://` 连接它。
{% end %}

### WebDAV 设置 {#webdav-settings}

点击 `WebDAV Settings...`。在你设置服务器之前，另外两个按钮会提示“Enter your WebDAV server in WebDAV Settings first.”（请先在 WebDAV Settings 中填写 WebDAV 服务器），并打开这个窗口。

| 字段 | 默认值 | 作用 |
| --- | --- | --- |
| `Server URL` | 空 | 你的存储的 WebDAV 地址。必须以 `http://` 或 `https://` 开头，例如 Nextcloud 的 `https://cloud.example.com/remote.php/dav/files/me/`。 |
| `Username`、`Password` | 空 | 你登录服务器的账号和密码。Throne 通过 HTTP 基本认证（basic authentication）发送它们，因此在 `http://` 下它们不会加密。 |
| `Folder` | `Throne` | 服务器地址下存放备份的文件夹。子文件夹之间用 `/` 分隔，例如 `Backups/Throne`。缺少的文件夹由 Throne 创建。留空表示 `Throne`。 |
| `Device name` | 电脑的名称 | 备份列表中这台电脑的备份所显示的名称。 |
| `Connect through the proxy` | 关闭 | 通过 Throne 的本地代理端口发送 WebDAV 请求。需要有正在运行的配置档。 |

`Test` 会测试窗口中的值，保存之前也可以测试。它会登录服务器，在文件夹不存在时创建它，并显示“Connected. The backup folder is ready.”（已连接，备份文件夹已就绪）或“Connection failed:”（连接失败）及原因。`Save` 保存这些值。

WebDAV 设置和设备名称只属于这台电脑。备份永远不会包含它们，恢复备份也不会改变它们。

不勾选 `Connect through the proxy` 时，WebDAV 请求遵循与订阅更新相同的规则：直接发出，除非开启了 `使用代理`（Use proxy）或 `系统代理`（System Proxy）。参见[网络请求](@/reference/privacy.zh.md#network-requests)。Throne 会检查服务器的 TLS 证书，除非开启了 `忽略 TLS 错误`（Ignore TLS errors）（`设置` → `基本设置` → `杂项`（Miscellaneous），仅在高级模式下显示，参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）。

### 上传备份 {#webdav-upload}

1. 在 `创建备份`（Create Backup）下勾选你需要的部分。
2. 点击 `Upload to WebDAV`。
3. Throne 会显示“Backup uploaded:”（备份已上传）以及新文件的名称。

每次上传都会生成一个新文件，名为 `throne_backup_<version>_<date>_<time>_<code>.thrbackup`，例如 `throne_backup_1.4.0_20261024_153000_a3f9.thrbackup`。日期和时间采用 UTC，最后四个字符是随机的，这样两台设备永远不会写入同一个文件。Throne 会在备份旁边维护备份列表 `throne_backups.json`。对每个备份，它会记录时间、大小、SHA-256 校验和、设备名称和一个随机的设备 ID、平台、Throne 版本以及包含的部分。如果只有这份列表未能更新，Throne 会提示“Backup uploaded, but the backup list could not be updated: …”（备份已上传，但备份列表未能更新）。备份本身已经在服务器上。

Throne 从不自行删除旧备份。请按下文所述删除你不再需要的备份。

### 恢复或删除备份 {#webdav-restore}

1. 点击 `Restore from WebDAV...`。Throne 会列出文件夹中的备份，最新的在最上面，列为 `Created`、`Device`、`Platform`、`Version`、`Parts` 和 `Size`。这台电脑的备份会显示“(this device)”（本设备）。
2. 选中一个备份并点击 `恢复`（Restore），或双击它。Throne 会下载它。
3. 恢复窗口随即打开。从[备份与恢复](#backup-restore)的第 2 步继续。

打开恢复窗口之前，Throne 会把下载的文件与备份列表中的大小和 SHA-256 校验和进行比较。如果不一致，它会停止并提示“The downloaded backup does not match the backup list; it may have been replaced or damaged.”（下载的备份与备份列表不一致，可能已被替换或损坏）。

列表会显示文件夹中的每个 `.thrbackup` 文件。不在备份列表中的文件，例如你自己复制进去的文件，会显示为“Unknown device”（未知设备），Throne 也无法检查它的下载。如果文件夹中没有备份，Throne 会提示“There are no backups in the WebDAV folder yet.”（WebDAV 文件夹中还没有备份）。

要删除备份，请选中它，点击 `Delete`，并确认“Delete this backup from the WebDAV server? This cannot be undone.”（要从 WebDAV 服务器删除此备份吗？此操作无法撤销）。Throne 会删除该文件及其在备份列表中的条目。

如果某个 WebDAV 操作失败，提示信息会说明原因：

| 提示 | 处理方法 |
| --- | --- |
| “Wrong username or password.” | 检查 `Username` 和 `Password`。启用了两步验证的服务通常需要在这里填写应用专用密码。 |
| “Permission denied.” | 该账号无权写入此文件夹。请选择其他 `Folder`，或检查服务器上的权限。 |
| “Not found.” | 该备份已不在服务器上，或者 `Server URL` 有误。请重新打开列表，或检查 `Server URL`。 |
| “The server redirected to …; check the server URL.” | 把提示中的地址填入 `Server URL`。上传、列出和删除时，Throne 不会跟随重定向。 |
| “Could not create the folder on the server: …” | 自己在服务器上创建该文件夹，或选择其他 `Folder`。 |
| “The server's folder listing could not be read.” | 该地址不是 WebDAV 地址，例如是你的存储的网页。请使用服务商提供的 WebDAV 地址。 |
| “The server did not answer in time.” | Throne 等待应答 30 秒，传输备份时等待 2 分钟。请检查连接，或尝试 `Connect through the proxy`。 |
| “有用代理的请求，但未启动配置档。”（Request with proxy but no profile started.） | 启动一个配置档，或关闭 `Connect through the proxy`、`使用代理` 和 `系统代理`。 |
| “Request with proxy but the mixed inbound is disabled and Tun Mode is off.” | `禁用混合入站`（Disable Mixed Inbound）已开启。请开启 `Tun 模式`（Tun Mode），或关闭代理选项。 |

### Android 上的 WebDAV {#webdav-android}

在 Throne for Android 中，打开侧边菜单中的 `工具`（Tools），或打开 `设置`（Settings）→ `备份与恢复`（Backup & restore）。`WebDAV`（WebDAV backup）卡片中有 `备份到 WebDAV`（Back up to WebDAV）、`从 WebDAV 恢复`（Restore from WebDAV）和 `WebDAV 设置`（WebDAV settings）。设置项包括 `服务器地址`（Server address）、`账号`（Username）、`密码`（Password）、`备份目录`（Backup path）（默认 `Throne`）和 `测试连接`（Test connection），自 2.0.4 起还有 `Device name`。`备份到 WebDAV` 会上传 `本地`（Local backup）卡片中勾选的部分。

要在两个应用之间共享备份，请在两边填写相同的服务器和相同的文件夹：桌面版的 `Folder` 对应 Android 上的 `备份目录`（Backup path）。在 Android 上恢复桌面版备份时，有几处会发生变化。参见[在桌面版与 Android 之间迁移](#desktop-android)。

Android 应用有几处不同：

- 在 2.0.3 中，它不使用备份列表。`从 WebDAV 恢复` 会显示文件夹中 `.thrbackup` 文件的名称，点按其中一个即可打开恢复窗口。对于 2.0.3 上传的备份，桌面版会显示为“Unknown device”。
- 自 2.0.4 起，它与桌面版使用同一份备份列表。`从 WebDAV 恢复` 会显示每个备份的日期、设备、平台、版本、内容和大小。点按某个备份可查看详细信息，然后选择 `恢复`（Restore）或 `Delete`。下载的文件会像桌面版一样根据列表进行检查。默认的 `Device name` 是 Android 设置中的设备名称，或者制造商和型号。
- 它没有用于 WebDAV 的代理选项。它的 WebDAV 请求从不使用本地代理。在 VPN 模式下，当 Throne 已连接时，这些请求会像其他应用的流量一样经过 VPN。
- 它从不忽略 WebDAV 的 TLS 证书错误。

## 在桌面版与 Android 之间迁移 {#desktop-android}

Throne 桌面版和 Throne for Android 使用相同的备份格式，因此你可以在两者之间双向迁移数据。

从桌面版迁移到手机：

1. 在桌面版中按上述方法创建备份。
2. 将 `.thrbackup` 文件复制到手机上。
3. 在手机上打开 `设置`（Settings）→ `备份与恢复`（Backup & restore），或打开侧边菜单中的 `工具`（Tools），点按 `恢复`（Restore）并选择该文件。从文件管理器中打开该文件时，也会提示恢复。
4. 勾选要恢复的部分并确认。Throne 会重启。

从手机迁移到桌面版：

1. 在手机上打开 `设置`（Settings）→ `备份与恢复`，勾选要备份的部分，然后点按 `创建备份`（Create backup）。`分享`（Share）可将文件发送到其他应用。
2. 将该文件复制到电脑上。
3. 在桌面版中按上述方法恢复。

你也可以通过 WebDAV 服务器迁移数据：在一个应用中上传备份，再在另一个应用中恢复它。参见 [WebDAV 备份](#webdav)。

在 Android 上恢复桌面版备份时，有几处会发生变化：

| 项目 | 在 Android 上的结果 |
| --- | --- |
| `OTP 配置档`、`IP lists and scans` 和 `自定义图标` | 不会恢复。Android 没有 IP 扫描器，因此分组的 `Server Address` 也不会恢复。 |
| 两个应用都有的设置 | 采用备份中的值。例如，TUN MTU 会变为 1500（Android 默认值：9000），日志级别会变为 `info`（Android 默认值：`warn`）。 |
| 仅 Android 才有的设置 | 保留当前值。 |
| 来自其他设备的连接 | 当你恢复 `设置`（Settings）部分，且备份允许其他设备连接而你的手机原先不允许时，Throne 会关闭 `允许来自局域网的连接`（Allow connections from the LAN）并显示警告。 |
| 使用 Android 不支持的条件的路由规则 | 会被跳过，并显示一条说明跳过数量的警告（自 2.0.2 起）。 |
| 原始路由配置档 | 会保留，但为只读。Android 无法使用它们；如果其中某个原本处于活动状态，则第一个可用的路由配置档会成为活动配置档。 |
| 路由配置档中的 OpenVPN 和 OpenConnect 端点 | 随配置档一起保留，但不会在 Android 上运行。 |
| Tailscale 和额外核心（Extra Core）配置档 | 会恢复，但无法在 Android 上启动。 |

反过来，匹配 Android 应用的规则以及分应用代理设置在桌面版上不起作用。

## 更新桌面版 {#updating}

你的配置档和设置存放在数据文件夹中，更新时会保留该文件夹（参见[数据文件夹](@/reference/files.zh.md#data-folder)）。尽管如此，仍请先创建备份。

1. 打开 `程序`（Program）→ `检查更新`（Check For Update）（1.4.0 之前为 `工具`（Tools）→ `检查更新`）。Throne 会向 GitHub 查询最新版本。如果你运行的已经是最新版本，会看到“无更新”。要查看你正在运行的版本，请打开 `程序` → `关于`（About）。
2. 如果有更新的版本，`更新`（Update）窗口会显示“Throne … 已发布，当前版本为 …。”（Throne … is available, you are using ….），并提供 `更新`（Update）、`在浏览器中打开`（Open in browser）和 `关闭`（Close）按钮。
3. 点击 `更新`（Update）。Throne 会下载新版本，并询问“更新已准备好，重启进行安装吗？”。
4. 点击 `是`（Yes）。Throne 会关闭，由更新程序安装新版本。

自 1.4.0 起，该窗口会显示从你当前版本之后直到新版本的每个版本的更改，内容取自 Throne 的更新日志。如果新版本是稳定版，其预发布版本不会单独列出，因为稳定版本身已列出它们的更改。如果 Throne 无法获取更新日志，窗口会显示“无法获取更新日志。”（Release notes are unavailable.）。更新说明中的链接会在浏览器中打开。`在浏览器中打开`（Open in browser）会打开 GitHub 上的发布页面。

一键 `更新`（Update）仅适用于 ZIP 版本，以及 Windows 安装程序为当前用户执行的默认安装——在这两种情况下，Throne 会把数据保存在程序旁边。其他安装方式只有 `在浏览器中打开`（Open in browser）按钮。当同一安装目录中运行着后台 Throne 时，窗口中没有 `更新` 按钮，并显示“从此安装目录运行的后台 Throne（PID …）仍在运行，无法更新；请先停止它。”（Update is unavailable while a background Throne (PID …) runs from this installation; stop it first.）。在 macOS 上、使用系统 Qt 的软件包以及通过 Linux 安装脚本安装的版本中，Throne 不附带更新程序，因此 `检查更新`（Check For Update）会显示为灰色。各种安装方式的更新方法如下：

| 安装方式 | 更新方法 |
| --- | --- |
| ZIP 文件 | 使用 `更新`，或下载新的 ZIP 并替换程序文件。保留 `config` 文件夹。 |
| Windows 安装程序，为当前用户安装（默认） | 使用 `更新`，或下载并运行新的安装程序。 |
| Windows 安装程序，为所有用户安装 | 下载并运行新的安装程序。 |
| `.deb` 或 `.rpm` 软件包 | 下载并安装新的软件包。 |
| Linux 安装脚本 | 再次运行该脚本。 |
| macOS | 下载新版本并替换 `Throne.app`。参见[安装](@/get_started/installation.zh.md#macos)。 |
| WinGet、Scoop、AUR、Nix、Homebrew，或 APT、RPM 仓库 | 使用包管理器更新。参见[包管理器](@/get_started/installation.zh.md#package-managers)。 |

如果你使用 Windows 安装程序卸载后重新安装，当卸载程序询问“Also delete your Throne profiles, settings and logs?”（是否同时删除你的 Throne 配置档、设置和日志？）时，请选择 `否`（No）。否则你的数据会被删除。

由于更新程序会替换程序文件，一些杀毒软件会将其标记为可疑。如果更新失败，请确认 Throne 不在你的“下载”（Downloads）文件夹中（[#1109](https://github.com/throneproj/Throne/issues/1109)），然后重试；或者从[下载页面](@/downloads.zh.md)下载新版本。

## Beta 版 {#beta}

Beta 版（预发布版本）会率先获得新功能和修复，但可能存在更多错误。安装 Beta 版之前请先备份。

- **桌面版：** 开启 `设置`（Settings）→ `基本设置`（Basic Settings）→ `杂项`（Miscellaneous）→ `允许更新到 beta 版`（Allow updating to beta versions）。在简洁模式下，该复选框位于 `通用`（Common）选项卡的 `网络设置`（Network Settings）部分（参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）。此后 `检查更新`（Check For Update）也会提供预发布版本；对于预发布版本，窗口标题为“更新（预发布）”（Update (Pre-release)）。
- **Android：** 开启 `设置`（Settings）→ `通用`（General）→ `允许更新到 Beta 版本`（Allow updating to beta versions）。此后 `关于`（About）→ `检查更新`（Check for updates）会显示“更新通道：正式版与预发布版”（Channel: stable and pre-releases）。参见[应用内更新程序](@/android/installation.zh.md#updater)。

如需退回，请关闭该选项。更新程序只提供比你当前运行的版本更新的版本，因此在更新的稳定版发布之前，你会一直停留在 Beta 版上，除非你自行安装稳定版。

## 旧版本 {#old-versions}

- **早于 1.1.0 的 Throne 桌面版。** 1.1.0 版本将所有数据移入了一个数据库文件 `throne.db`。旧的配置不会被转换。请重新添加你的订阅和配置档，或者试试 [#1202](https://github.com/throneproj/Throne/issues/1202) 中的社区脚本。`备份和恢复`（Backup and Restore）自 1.1.3 起提供，因此 1.1.3 及更高版本创建的备份可以在更新的版本中恢复。
- **Nekoray 或 NekoBox。** 参见[从 Nekoray / NekoBox 迁移](@/help/migrating.zh.md)。
- **早于 2.0.0 的 Throne for Android。** 2.0.0 版本使用新的密钥签名，因此无法覆盖安装在旧版本之上。请先卸载旧应用；2.0.0 会以空白状态启动。旧的 Android 备份（`throne_backup_*.json` 文件和 WebDAV `.zip` 文件）无法恢复，只能恢复 `.thrbackup` 文件。卸载前，请记下你的订阅链接。如果你同时使用 Throne 桌面版，也可以改用桌面版备份来迁移全部数据。参见[安装与升级](@/android/installation.zh.md#upgrading)。

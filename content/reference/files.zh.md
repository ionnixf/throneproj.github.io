+++
title = "命令行、文件与日志"
description = "Throne 桌面版的命令行选项、它存放数据和日志的位置，以及如何重置。"
weight = 30
toc = true
+++

## 命令行 {#command-line}

| 选项 | 作用 |
| --- | --- |
| `-appdata` | 将数据保存在你的用户文件夹中，而不是程序旁边。参见[数据文件夹](#data-folder)。 |
| `-appdata <folder>` | 将数据保存在 `<folder>` 中的 `config` 文件夹里。 |
| `-tray` | 启动到托盘，窗口保持隐藏。 |
| 一条 `throne://` 链接 | 像你点击了该链接一样处理它。参见[深度链接](@/advanced/deeplinks.zh.md)。 |
| 文件路径或 `file://` URL | 导入这些文件，效果与 `程序`（Program）→ `添加文件中的配置档`（Add profile from File(s)）相同。 |
| `--headless` | 以无窗口方式运行 Throne，作为服务或在容器中运行。参见[无界面 Throne](@/headless/_index.zh.md)；其选项见[命令行与数据目录](@/headless/command_line.zh.md)。 |
| `--check`、`--health`、`--print-connection` | 检查这台电脑能否运行无界面 Throne、询问正在运行的实例的状态，或打印 `thronectl` 所需的设置。 |

```bash
./Throne -appdata "$HOME/throne-test"
```

```powershell
.\Throne.exe -appdata D:\ThroneData
```

- 每个数据文件夹只能运行一个 Throne。再次启动时，新实例会把链接或文件交给正在运行的 Throne，并将其窗口调到前台。如果某个无界面 Throne 正在使用该文件夹，新启动的实例会显示“Throne is running in the background”（Throne 正在后台运行）并退出；参见[故障排除](@/help/troubleshooting.zh.md#background-throne)。
- **macOS：** 要传递选项，请运行应用包内的程序：`/Applications/Throne.app/Contents/MacOS/Throne -tray`。

## 数据文件夹 {#data-folder}

Throne 将你的数据保存在一个名为 `config` 的文件夹中。`设置`（Settings）→ `打开配置文件夹`（Open Config Folder）可以打开它（仅在高级模式下可用，参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）。

| Throne 的安装方式 | 数据文件夹 |
| --- | --- |
| Windows 或 Linux ZIP | 解压出的 `Throne` 文件夹中的 `config`（便携） |
| Windows 安装程序，为当前用户安装（默认） | 安装文件夹中的 `config`（便携），默认为 `%LOCALAPPDATA%\Throne\config` |
| Windows 安装程序，为所有用户安装 | 每个用户各自的 `%LOCALAPPDATA%\Throne\config` |
| Linux `.deb`、`.rpm` 或安装脚本 | `~/.config/Throne/config`；如果你设置了 `XDG_CONFIG_HOME`，则为 `$XDG_CONFIG_HOME/Throne/config` |
| macOS | `~/Library/Preferences/Throne/config` |

- 便携版以 `-appdata` 启动时会使用用户专属文件夹：Windows 上为 `%LOCALAPPDATA%\Throne\config`，Linux 上为 `~/.config/Throne/config`。如果它无法写入自己的文件夹（例如位于 `Program Files` 中），也会自动切换过去：只复制一次现有的 `config` 文件夹，此后即使你以管理员身份运行，也会继续使用这个用户专属文件夹。
- 来自其他来源的软件包（WinGet、Scoop、AUR、Nix）可能使用其他文件夹。`打开配置文件夹`（Open Config Folder）总会打开正确的那个。
- Xray 的 geo 文件 `geoip.dat` 和 `geosite.dat`，以及 `diagnostics` 文件夹（来自 `基本设置`（Basic Settings）→ `诊断`（Diagnostics）），位于 `config` 文件夹旁边，而不是其中。

### 配置文件夹中的文件 {#config-files}

| 文件或文件夹 | 内容 |
| --- | --- |
| `throne.db` | 配置档、分组、路由配置档、OTP 配置档和设置（SQLite）。Throne 运行期间，它旁边会出现 `throne.db-wal` 和 `throne.db-shm`。 |
| `throne_stats.db` | `工具`（Tools）→ `流量统计`（Traffic Stats）所用的流量历史：48 小时的每分钟数据和 90 天的每小时数据。`基本设置` → `样式`（Style）→ `禁用流量聚合`（Disable Traffic Aggregation）可以停止记录。 |
| `cache.db` | 核心的缓存：已下载的规则集、每个自动选择器上一次的选择；如果开启了 `保存缓存到文件`（Save Cache To File），还包括 DNS 缓存。 |
| `logs/` | 日志文件，参见[日志文件](#log-file)。 |
| `crashes/` | 崩溃转储（仅限 Windows）。保留最新的 5 个。 |
| `icons/` | `基本设置` → `样式` → `启用自定义图标`（Enable Custom Icons）所用的自定义托盘图标：`Off.png`、`Throne.png`（有配置档运行）、`Proxy.png`、`Tun.png` 和 `Connecting.png`。 |
| `hwid` | 仅限 Linux：由于系统中没有可用的 `/etc/machine-id` 或 `/var/lib/dbus/machine-id`，Throne 自行创建的设备 ID。参见 [HWID](@/reference/privacy.zh.md#hwid)。 |
| `.lock` | 表示有一个 Throne 正在使用此文件夹。Throne 从不删除它。 |
| `dashboard/` | 用于 Clash API 的网络仪表盘。请将占位页面替换为仪表盘文件。 |
| `sb-dashboard/` | `工具` → `打开网络仪表盘`（Open Web dashboard）所打开的网络仪表盘。 |

要把数据迁移到另一台电脑或 Android 上，请使用 `.thrbackup` 备份，而不是复制文件。参见[备份与恢复](@/guides/backup.zh.md#backup-restore)。

## 日志 {#logs}

主窗口底部的 `日志`（Logs）标签页显示来自 Throne 和核心的消息。

日志设置位于 `设置`（Settings）→ `基本设置`（Basic Settings）→ `日志`（Logging）（仅在高级模式下可用）：

| 设置 | 默认值 | 作用 |
| --- | --- | --- |
| `最多日志行数`（Max log lines） | `500` | `日志` 标签页保留的行数。 |
| `Sing-box 日志级别`（Sing-box Log level） | `info` | 核心写入日志的详细程度：`trace`、`debug`、`info`、`warn`、`error`、`fatal` 或 `panic`。 |
| `Xray 日志级别`（Xray Log level） | `warning` | Xray 写入日志的详细程度：`debug`、`info`、`warning`、`error` 或 `none`。 |
| `日志筛选`（Log Filtering） | 关闭 | `启用「包含」规则`（Enable Include Rules）只显示与你的关键词或正则表达式匹配的行，`启用「排除」规则`（Enable Exclude Rules）会隐藏它们。被隐藏的行仍会写入日志文件。 |

要为 Bug 报告记录详细日志，请按照[报告 Bug](@/help/bug_reports.zh.md#logs) 中的说明操作。

### 日志文件 {#log-file}

Throne 还会把更详细的日志写入配置文件夹中的 `logs/throne.log`。

- Throne 每次启动都会新建一个文件。文件达到 4 MB 时，Throne 会将其重命名为 `throne.log.1` 并开始写入新文件；最多保留三个旧文件（`throne.log.1` 到 `throne.log.3`）。
- 如果 Throne 没有正常退出，那次会话的日志会以 `crashed-YYYYMMDD-HHMMSS.log` 的名称保留（最新的 5 个），下次启动时，`日志` 标签页会显示一条带有日志文件夹路径的警告。

{% alert_warning() %}
日志中可能包含你访问过的域名和你的服务器地址。公开分享日志之前，请先检查一遍。
{% end %}

**Android：** 在侧边菜单中打开 `日志`（Logs）。`分享日志`（Share logs）和 `保存日志…`（Save logs…）可以导出日志，默认会隐藏敏感数据。日志级别位于 `设置`（Settings）→ `核心`（Core）→ `日志级别`（Log level），默认为 `warn`。参见 [Android 故障排除](@/android/troubleshooting.zh.md#logs)。

## 重置 Throne {#reset}

Throne 没有重置按钮。要使用默认设置从头开始，请给 Throne 一个全新的空配置文件夹：

1. 如果想保留某些内容，请先创建备份：`设置`（Settings）→ `基本设置`（Basic Settings）→ `备份和恢复`（Backup and Restore）→ `创建备份...`（Create Backup...）。
2. `设置`（Settings）→ `打开配置文件夹`（Open Config Folder）会显示该文件夹的位置。
3. 通过托盘菜单或 `程序`（Program）菜单中的 `退出`（Exit）退出 Throne。仅关闭窗口是不够的。
4. 重命名 `config` 文件夹，例如改为 `config-old`。
5. 启动 Throne。它会创建一个使用默认设置的新 `config` 文件夹。

要恢复部分旧数据，请在同一标签页中使用 `从备份恢复...`（Restore from Backup...），并只选择这些部分。要撤销重置，请退出 Throne，删除新的 `config` 文件夹，并将 `config-old` 改回 `config`。

重置不会改变存放在配置文件夹之外的内容：`随系统启动`（Start with system）（操作系统中的自启动项）、`throne://` 链接的注册，以及 Xray geo 文件。

**Windows：** 卸载用安装程序安装的 Throne 时，可以选择同时删除配置文件夹。

**Android：** `设置`（Settings）→ `恢复默认设置`（Restore default settings）会重置所有设置，但保留你的配置档、分组和路由配置档，随后应用会重新启动。要删除所有内容，请在 Android 的应用设置中清除该应用的数据。文件管理器无法打开应用的数据，因此请使用 `.thrbackup` 备份把数据复制出来。

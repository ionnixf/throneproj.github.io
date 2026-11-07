+++
title = "命令行与数据目录"
description = "无界面 Throne 的选项、环境变量、数据目录、重启模式和退出码。"
weight = 30
toc = true
+++

桌面版的选项请参见[命令行](@/reference/files.zh.md#command-line)。

## 选项与变量 {#options}

`Throne --headless --help` 会列出所有选项。选项优先于对应的变量。使用 systemd 单元时，请在 `/etc/throne/throne.env` 中设置变量，用 drop-in 文件设置选项（参见 [Linux 软件包](@/headless/installation.zh.md#linux-packages)）。

| 选项 | 变量 | 作用 |
| --- | --- | --- |
| `--headless` | `THRONE_HEADLESS=1` | 不带窗口运行。 |
| `--data-dir DIR` | `THRONE_DATA_DIR` | [数据目录](#data-dir)。 |
| `--restart-mode MODE` | `THRONE_RESTART_MODE` | `auto`（默认）、`exit` 或 `exec`。参见[重启模式](#restart-modes)。 |
| `--no-resume` | `THRONE_RESUME=0` | 启动时不启动上次的配置档。 |
| `--no-kill-switch` | `THRONE_KILL_SWITCH=0` | 本次运行让 [Kill Switch](@/advanced/kill_switch.zh.md) 保持关闭。在 `kill_switch` 设置关闭之前，配置档仍会被拒绝。用于[恢复访问](@/headless/recovery.zh.md#recovery)。 |
| `--log-format FORMAT` | `THRONE_LOG_FORMAT` | `auto`（默认）、`journal` 或 `plain`。参见[日志](@/headless/monitoring.zh.md#logs)。 |
| `--api-listen LISTEN` | `THRONE_API_LISTEN` | `PORT`（在 `127.0.0.1` 上）、`IPv4:PORT`、`[IPv6]:PORT`、`*:PORT` 或 `off`。默认：`127.0.0.1:9095`，或已保存的设置所指定的地址。 |
| `--api-key-file FILE` | `THRONE_API_KEY_FILE` | 从这个已存在的文件读取 API 密钥（`thronectl keygen -o FILE` 可以生成一个）。默认：数据目录中的 `config/api.key`。 |
| `--api-admin-lan` | `THRONE_API_ADMIN_LAN=1` | 也响应来自网络的[管理请求](@/headless/thronectl.zh.md#remote)。 |
| `--api-allow LIST` | `THRONE_API_ALLOW` | 可以使用非环回监听器的地址和子网，以逗号或空格分隔。不设置即允许所有地址；空列表会以退出码 64 退出。 |
| `--debug` | | 调试日志。 |
| `--service` | | 仅限 Windows：在服务控制管理器下运行。 |
| `--health` | | 询问正在运行的 Throne [状态如何](@/headless/monitoring.zh.md#health)。 |
| `--print-connection` | | 为 [thronectl](@/headless/thronectl.zh.md#connect) 打印 `THRONE_ADDR` 和 `THRONE_KEY_FILE`。 |
| `--check` | | [检查](@/headless/monitoring.zh.md#check)这台电脑能否运行无界面 Throne。 |

- `THRONE_RESUME`、`THRONE_KILL_SWITCH` 和 `THRONE_API_ADMIN_LAN` 接受 `1`、`true`、`yes`、`on`、`0`、`false`、`no` 或 `off`。其他任何值都会以退出码 64 退出。
- `--api-*` 选项和变量会在每次启动时覆盖已保存的 API 设置，并且从不保存，因此错误的 API 设置总能通过命令行修正。
- `--health`、`--print-connection` 和 `--check` 不需要 `--headless` 也能使用。一次只能使用其中一个，并且不能与 `--service` 一起使用。
- 在命令行中传入链接或文件时，会以退出码 64 退出。请改用 [`thronectl import`](@/headless/thronectl.zh.md#profiles) 导入。

## 数据目录 {#data-dir}

无界面 Throne 使用以下各项中第一个适用的：

1. `--data-dir`，然后是 `THRONE_DATA_DIR`。
2. `STATE_DIRECTORY`，systemd 会为 `StateDirectory=` 设置它。
3. 以 root 身份运行时：Linux 上为 `/var/lib/throne`，macOS 上为 `/Library/Application Support/Throne`。在 Windows 上，作为服务或以 LocalSystem 身份运行时：`C:\ProgramData\Throne`。
4. 桌面版的文件夹：Linux 上为 `~/.config/Throne`，macOS 上为 `~/Library/Preferences/Throne`，Windows 上为 `%LOCALAPPDATA%\Throne`。

它从不使用便携版安装在程序旁边的文件夹；请用 `--data-dir` 传入该文件夹。

在 Linux 和 macOS 上，Throne 会把缺失的目录创建为只有其用户可以读取。以 root 身份运行时，它会拒绝其他用户可以写入的数据目录（退出码 78），因为设置中指定了 Throne 会启动的程序。Windows 请参见[数据文件夹](@/headless/installation.zh.md#windows-data-folder)。

| 路径 | 内容 |
| --- | --- |
| `config/throne.db` | 配置档、分组、路由配置档和设置，与桌面版相同 |
| `config/api.key` | API 密钥，只有 Throne 的用户可以读取 |
| `config/logs/throne.log` | 本次运行的日志。`crashed-<time>.log` 保存未正常结束的那次运行的日志。 |
| `config/crashes/` | 崩溃报告 |
| `config/hwid` | 系统没有设备 ID 时使用的[设备 ID](@/headless/_index.zh.md#device-id) |
| `web/` | 仅限 Docker 镜像：[Web 界面](@/headless/web.zh.md#docker)的密码哈希和 TLS 证书 |

## 重启模式 {#restart-modes}

`thronectl restart`、恢复备份和 `SIGHUP` 都会重启 Throne。`--restart-mode` 决定重启的方式：

| 模式 | 会发生什么 |
| --- | --- |
| `exit` | Throne 以退出码 75 退出，由服务管理器再次启动它。 |
| `exec` | Throne 在同一个进程中重新启动（Linux、macOS）。在 Windows 上，它会启动一个新的 Throne 进程，并以退出码 0 退出。 |
| `auto` | 在 systemd、launchd 或容器的 init 下，或者作为 Windows 服务运行时，使用 `exit`。否则使用 `exec`，例如在终端中。 |

随附的单元、plist 和 Docker 镜像需要 `auto` 或 `exit`；Windows 服务始终使用 `exit`。`Throne --check` 会显示 `auto` 选择了哪种模式。在终端中运行时，关闭终端会让 Throne 以退出码 0 停止，而不是重启它。

## 退出码 {#exit-codes}

Throne 在退出之前会向日志写入 `EXIT <code>: <reason>`。

| 退出码 | 含义 |
| --- | --- |
| 0 | 已停止：`SIGTERM`、`SIGINT`、`thronectl shutdown`、Windows 服务停止或 `Ctrl+C` |
| 1 | 启动因可能会自行消失的原因而失败：锁、数据库、核心的 IPC 监听器，或者 `--api-listen` 地址在 30 秒内没有出现 |
| 3 | 另一个 Throne 正在使用该数据目录 |
| 64 | 错误的选项、选项值或变量 |
| 70 | 核心反复失败，或者在 60 秒内一直不可用 |
| 71 | 某个配置档处于连接中的状态达 180 秒，或处于停止中的状态达 90 秒 |
| 75 | 请求了重启，且处于 `exit` 模式或作为 Windows 服务运行 |
| 78 | 配置错误：数据目录（权限不安全，或者没有可用来找到它的 `HOME`）、API 密钥文件、`--api-listen` 监听器，或者无法使用的数据库文件 |

随附的服务文件会这样处理：

- **systemd 单元：** 在退出码 1、70、71 和 75 之后，3 秒后再次启动 Throne；在 0、3、64 和 78 之后让它保持停止。它没有启动次数限制（`StartLimitIntervalSec=0`）：每次重启、恢复和 `SIGHUP` 都是一次新的启动，如果有限制，Throne 可能会停在停止状态，并且没有 API 可用。
- **Docker**（`restart: unless-stopped`）：每次退出后都会再次启动它，`docker stop` 除外。
- **launchd plist：** 除 0 以外的任何退出码之后都会再次启动它，最多每 10 秒一次，因此 3、64 和 78 也会被重试。
- **Windows 服务：** 除 0 以外的任何退出码之后都会运行它的[恢复操作](@/headless/installation.zh.md#windows)。

连续三次启动都没有可用的核心之后，Throne 不再以退出码 70 或 71 退出，而是[保持运行](@/headless/recovery.zh.md#protection)。对于与数据库相关的退出码 78，日志会说明该怎么做：把损坏的 `config/throne.db` 连同它的 `-wal` 和 `-shm` 文件一起移到别处，或者放回一份副本。让只读文件可被 Throne 的用户写入。

检查类命令有各自的退出码，遇到错误的选项时以 64 退出：

| 命令 | 0 | 1 | 2 |
| --- | --- | --- | --- |
| `--health` | 健康 | 不健康，或仍在启动中 | 没有 Throne 回应，或者 Windows 命令行窗口未以管理员身份运行 |
| `--check` | 没有 `[FAIL]` 行 | 有 `[FAIL]` 行 | |
| `--print-connection` | 已打印两行 | | API 已关闭或未在监听，其密钥不在文件中，或者 Windows 命令行窗口未以管理员身份运行 |

当 `--health` 和 `--print-connection` 找不到数据目录（没有 `HOME`）时，会以退出码 78 退出；请用 `--data-dir` 指定它。

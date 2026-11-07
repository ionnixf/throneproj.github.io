+++
title = "故障排除"
description = "无界面 Throne 的常见问题，从无法启动的单元到 TUN 冲突和被拒绝的 API 请求，以及 Bug 报告中应包含的内容。"
weight = 60
toc = true
+++

首先运行 `Throne --check` 和 `Throne --health`，并阅读日志的最后几行（参见[健康检查与日志](@/headless/monitoring.zh.md)）。`EXIT <code>: <reason>` 这一行说明了 Throne 退出的原因；参见[退出码](@/headless/command_line.zh.md#exit-codes)。大多数错误消息本身就说明了解决方法。下表列出的是没有说明的那些。

## 常见问题 {#symptoms}

| 症状 | 解决方法 |
| --- | --- |
| 退出码 3：`… is in use by …`，或者桌面版提示“Throne 正在后台运行”（Throne is running in the background） | 两个 Throne 实例共用一个数据目录，例如桌面版和用户单元。请停止其中一个（`systemctl --user stop throne`），或者用 `thronectl` 管理无界面 Throne。参见[与桌面版 Throne 同时运行](@/headless/_index.zh.md#beside-desktop)。 |
| `--print-connection`：`The API key is not in a file` | 密钥保存在设置中，就像桌面版那样。请把该密钥交给 thronectl（在桌面版中：`Integration Settings` → `Throne API` → `Copy for scripts`），或者用 `--api-key-file` 启动 Throne。 |
| thronectl：`forbidden` | 来自其他设备的[管理请求](@/headless/thronectl.zh.md#remote)。请用 `--api-admin-lan` 启动 Throne，或者在 Throne 所在的设备上运行该命令。 |
| thronectl：无法连接 | Throne 没有运行，或者 `THRONE_ADDR` 指向了别处。请检查 `systemctl status throne` 和 `Throne --print-connection`。 |
| `thronectl tun on`：`needs_privileges` | 请运行 `Throne --check`，并参见 [Linux 上的权限](@/headless/installation.zh.md#linux-privileges)。 |
| `tun_conflict` | 其他网络使用了 Throne 的 TUN 地址范围：请换一个空闲的范围，例如 `thronectl settings set tun_ipv4_cidr=172.29.0.1/24`。另一个 Throne 或 VPN 正在运行 TUN：同一时间只能有一个，请停止它。另一个程序占用了 `dns_in_port`：请设置另一个端口。然后再次运行 `thronectl tun on`。 |
| 配置档被拒绝：`kill_switch_inactive` | Kill Switch 已开启，但无法工作。`thronectl --json status` 中的 `kill_switch_error` 会说明原因：`privilege`、`unsupported`、`config` 或 `overridden`。`thronectl settings set kill_switch=false` 可以将其关闭。参见 [Kill Switch](@/advanced/kill_switch.zh.md)。 |
| `degraded: vpn_challenge_pending` 或 `vpn_auth_failed` | VPN 配置档需要无界面 Throne 无法提供的登录，或者其已保存的凭据被拒绝。请为该配置档关联一个 [OTP 条目](@/advanced/vpn_profiles.zh.md#otp-manager)，或者在桌面版 Throne 中修正凭据，然后用[备份](@/headless/thronectl.zh.md#from-desktop)迁移过来。 |
| 其他 `degraded:` 值 | 参见[取值列表](@/headless/monitoring.zh.md#health)。 |
| Docker：发布的端口 2080 没有响应 | 入站监听的是 `127.0.0.1`。请运行 `thronectl settings set inbound_address=0.0.0.0`，然后重新启动配置档。 |
| Docker：`thronectl shutdown` 之后容器又回来了 | `restart: unless-stopped` 会再次启动它。请使用 `docker compose stop`。 |
| macOS：Throne 每 10 秒启动一次又退出 | launchd 在不断重试一个配置错误。请阅读 `/Library/Logs/Throne/throne.log`。 |
| Windows：服务立即停止 | `sc.exe query Throne` 会把 Throne 的退出码显示为 `SERVICE_EXIT_CODE`；日志位于 `C:\ProgramData\Throne\config\logs`。退出码 78 通常表示[数据文件夹](@/headless/installation.zh.md#windows-data-folder)有问题。 |
| Windows：`tun_failed` 且含有 `open existing adapter` | Windows 仍占用着一次未正常结束的运行留下的 TUN 适配器。请运行 `thronectl connect last`；如果一直失败，请重启 Windows。 |
| Kill Switch 或 TUN 把你锁在外面 | 用 `--no-resume --no-kill-switch` 启动 Throne 一次。参见[恢复访问](@/headless/recovery.zh.md#recovery)。 |

## Bug 报告中应包含的内容 {#reporting}

如果问题仍然存在，请[报告 Bug](@/help/bug_reports.zh.md)，并附上：

- `Throne --check` 和 `Throne --health` 的输出；
- Throne 的运行方式：软件包或镜像、其版本以及服务管理器；
- 从启动到出现问题为止的日志，如果能复现问题，请使用 `--debug`；如果是崩溃，还请附上 `config/logs/crashed-<time>.log`。

发布之前，请删除其中的服务器地址、订阅 URL 和密钥。切勿发布 `api.key`。

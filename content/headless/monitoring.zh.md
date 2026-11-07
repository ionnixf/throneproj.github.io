+++
title = "健康检查与日志"
description = "检查一台电脑能否运行无界面 Throne，询问正在运行的 Throne 状态如何，并找到它的日志。"
weight = 40
toc = true
+++

## Throne --check {#check}

`Throne --check` 会告诉你这台电脑能否按当前的设置运行无界面 Throne。请以 Throne 的用户身份运行它，并使用 Throne 启动时所用的选项和变量。它不会更改任何内容，并且可以与正在运行的 Throne 同时运行。

```text
$ sudo /opt/Throne/Throne --check
[INFO] data dir /var/lib/throne (root default): does not exist yet; the first start creates it
[ OK ] port 127.0.0.1:9095 (the Throne API, the headless default): free
...
result: 0 failed, 0 warnings
```

它检查数据目录、设置、核心、TUN、Kill Switch、API 密钥、端口、重启模式和[设备 ID](@/headless/_index.zh.md#device-id)，有解决方法时还会打印 `fix:` 行。只有会让 Throne 完全无法工作的问题才标记为 `[FAIL]`，此时该命令以退出码 1 退出。缺少 TUN 或 Kill Switch 所需的权限会标记为 `[WARN]`，并附上用于修复的 `setcap` 或 `chmod u+s` 命令；在容器中则附上 `--cap-add NET_ADMIN` 和 `--device /dev/net/tun`。

## Throne --health {#health}

`Throne --health` 会询问正在运行的 Throne，并打印一行 JSON。请以 Throne 的用户身份、针对同一个数据目录运行它：

```bash
$ sudo /opt/Throne/Throne --health
{"api":"listening","core_restarts":0,"degraded":null,"headless_dialogs":0,"ok":true,"previous_crash":false,"previous_exit":{"at":1760700000,"code":75,"reason":"restart requested"},"ready":true,"state":"running","stuck_s":0}
```

Throne 健康时，它以退出码 0 退出；其他退出码请参见[退出码](@/headless/command_line.zh.md#exit-codes)。Docker 镜像使用它作为健康检查。

| 字段 | 含义 |
| --- | --- |
| `ok` | Throne 已完成启动、核心已连接、没有卡住的操作，并且 Kill Switch 处于关闭状态或正常工作。它从不取决于远程服务器或正在运行的配置档，因此在它为 `false` 时重启 Throne，不会在服务器故障期间陷入循环。 |
| `ready` | Throne 已完成启动。 |
| `state` | `starting`、`idle`、`connecting`、`running` 或 `stopping`。 |
| `degraded` | `null`，或者 Throne 无法按设置工作的原因（参见下文）。 |
| `stuck_s` | 某个配置档在连接中超出 90 秒的秒数，或在停止中超出 45 秒的秒数。只要它大于 0，`ok` 就为 `false`。 |
| `headless_dialogs` | 因为没有窗口而跳过的询问。它应保持为 0；日志会在 `HEADLESS-DIALOG` 行中列出它们。 |
| `previous_exit` | 上一次运行是如何结束的，格式为 `{code, reason, at}`，或者为 `null`。如果上一次运行没有正常结束，`previous_crash` 为 `true`。 |
| `api` | API 监听器：`off`、`listening`、`key_invalid`、`failed`、`retrying`（最多等待 30 秒，等其地址出现）或 `address_missing`。 |

`degraded` 的取值，按多个值同时适用时 Throne 报告的顺序排列：

| 值 | 含义 |
| --- | --- |
| `core_gave_up` | 核心反复失败。Throne 会以退出码 70 退出；或者在连续三次这样的启动之后，[保持运行](@/headless/recovery.zh.md#protection)，并每 5 分钟重试一次核心。 |
| `core_unavailable` | 核心进程没有运行。 |
| `kill_switch_failed` | Kill Switch 已开启，但失败了，因此配置档会被拒绝。参见 [Kill Switch](@/advanced/kill_switch.zh.md)。 |
| `api_unavailable` | API 已开启，但没有在监听，例如因为另一个程序占用了其端口，或者其密钥文件无法使用。`ok` 保持为 `true`。 |
| `tun_unavailable` | Throne 上次停止时 TUN 处于开启状态，而现在无法开启：Throne 缺少权限，或者有其他东西占用了 TUN（[TUN 冲突](@/headless/_index.zh.md#beside-desktop)）。该配置档没有启动。请运行 `thronectl tun off`，然后运行 `thronectl connect`。 |
| `resume_suppressed` | Throne 没有恢复上次的配置档，因为在最近三次恢复中，每一次 Throne 或其核心都在一分钟内停止了。请用 `thronectl connect` 启动该配置档。 |
| `vpn_auth_failed` | VPN 配置档的服务器拒绝了其已保存的凭据。该配置档停止时，此状态会清除。 |
| `vpn_challenge_pending` | VPN 配置档的服务器要求提供 Throne 无法自行回答的内容，例如网页登录。Throne 最多在 5 分钟后将其取消。 |

详情请参见 [API 规范](https://github.com/throneproj/Throne-api/blob/main/spec/api.md#headless-throne)。如需更多字段，例如 `pid`、`version` 以及 `thronectl status` 的各个字段，脚本可以向数据目录中的套接字 `config/.instance` 发送 `HEALTH`（Windows 除外）：

```bash
echo HEALTH | sudo socat -t 3 - UNIX-CONNECT:/var/lib/throne/config/.instance
```

## systemd：就绪、状态与看门狗 {#systemd}

随附的单元为 `Type=notify`：

- **`READY=1`**：在 Throne 已持有数据目录、数据库已打开、API 已在监听、核心已连接，并且 Kill Switch 的状态已确定时发送，通常不到 20 秒。在此之前，Throne 会延长启动超时，因此 `TimeoutStartSec=90` 只会终止卡住的启动。
- **`STATUS=`**：`systemctl status` 会显示它，例如 `Idle`、`Connected <profile> (Tun)` 或 `Degraded: <reason>`。
- **`WATCHDOG=1`**：每 20 秒发送一次（`WatchdogSec=60`）。如果主循环卡住，systemd 会发送 `SIGABRT` 并再次启动 Throne。

必须使用 `KillMode=mixed`：systemd 只向 Throne 发送 `SIGTERM`，Throne 会依次停止其核心和 Kill Switch，然后退出（`TimeoutStopSec=45`）。第二个 `SIGTERM` 会立即杀死核心。不要使用 `systemctl kill`：它会直接向核心和 Kill Switch 辅助程序发送信号。

## 日志 {#logs}

Throne 会把日志写到标准错误输出，以及数据目录中的 `config/logs/throne.log`：

| Throne 的运行方式 | 日志 |
| --- | --- |
| systemd 系统单元 | `journalctl -u throne -f` |
| systemd 用户单元 | `journalctl --user -u throne -f` |
| Docker | `docker compose logs -f throne` |
| macOS LaunchDaemon | `/Library/Logs/Throne/throne.log` |
| Windows 服务 | `C:\ProgramData\Throne\config\logs\throne.log` |

`thronectl logs -n 500` 通过 API 打印最后 500 行，`thronectl logs -f` 则会继续跟踪新行，Throne 重启后也不会中断。这是一个[管理请求](@/headless/thronectl.zh.md#remote)。Throne 保留最新的 5000 行，包括核心的输出；[Web 界面](@/headless/web.zh.md)的 `Logs` 页面也会显示它们。

`--debug` 会添加调试行。`--log-format` 设置标准错误输出的格式：`journal`（供 journald 使用的 syslog 优先级）、`plain`（本地时间），或者 `auto`，它会在标准错误输出发往 systemd 日志（journal）时选择 `journal`。

值得搜索的日志行：

- `EXIT <code>: <reason>`：Throne 退出的原因。参见[退出码](@/headless/command_line.zh.md#exit-codes)。
- `Ready <n> ms after launch`：启动已完成。
- `[Resume] …`：上次的配置档是否已再次启动。
- `[Kill switch] Not active (<code>): …`：Kill Switch 无法工作的原因。
- `HEADLESS-DIALOG`：一个无人能回答的询问。

当某次运行没有正常结束时，它的日志会保存为 `config/logs/crashed-<time>.log`，崩溃报告则保存到 `config/crashes/`。

+++
title = "Throne API 与 thronectl"
description = "通过 Throne API 及其命令行客户端 thronectl，从脚本、热键和其他设备控制和管理 Throne。"
weight = 80
toc = true
+++

Throne API 让脚本、热键工具和其他设备可以控制和管理 Throne 桌面版。`thronectl` 是该 API 的命令行客户端，`thronectl web` 还提供一个 [Web 界面](@/headless/web.zh.md)。要编写自己的客户端，请使用 [Throne-api](https://github.com/throneproj/Throne-api) 仓库中的 Go 库和 [API 规范](https://github.com/throneproj/Throne-api/blob/main/spec/api.md)。

[无界面 Throne](@/headless/_index.zh.md) 从一开始就开启了 API，参见[使用 thronectl 管理](@/headless/thronectl.zh.md)。Throne for Android 没有 Throne API。

## 在 Throne 中开启 API {#enable}

1. 打开 `设置`（Settings）→ `Integration Settings`（仅限高级模式，参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)），然后打开 `Throne API` 标签页。
2. 勾选 `Enable the Throne API`。Throne 会填入一个新的密钥。
3. 点击 `确定`（OK）。

API 监听 `127.0.0.1:9095`，因此只有这台电脑上的程序可以连接。要让其他设备也能连接，请把 `Listen on` 设为 `Local network`，并在 `Allowed addresses` 中列出这些设备，例如 `192.168.1.0/24, 10.0.0.5`（留空则允许所有地址连接）。只有当这些设备还需要发送[管理请求](#admin-requests)时，才勾选 `允许从局域网进行管理`（Allow management from the local network）。

`Copy for scripts` 会复制 `thronectl` 读取的两项设置：`THRONE_ADDR` 和 `THRONE_KEY`。`Regenerate` 会生成新密钥，使用旧密钥的脚本随即停止工作。[断网保护（kill switch）](@/advanced/kill_switch.zh.md)不会阻止 API 客户端。

## 安装 thronectl {#install-thronectl}

Throne 的 `.deb` 和 `.rpm` 软件包、Linux ZIP 包以及无界面 Throne 的 Docker 镜像都包含 `thronectl`。其他情况下，请从 [Throne-api 的发布页面](https://github.com/throneproj/Throne-api/releases)下载适用于 Linux、Windows、macOS 或 FreeBSD 的版本，并把它放到 `PATH` 中的某个文件夹里，或者用 Go 安装：

```bash
go install github.com/throneproj/Throne-api/cmd/thronectl@latest
```

## 将 thronectl 连接到 Throne {#connect}

`thronectl` 从 `THRONE_ADDR`（默认 `127.0.0.1:9095`）读取地址，从 `THRONE_KEY` 或 `THRONE_KEY_FILE` 指定的文件读取密钥。它从不接受以参数形式给出的密钥，因此请把密钥保存在只有你能读取的文件中：

1. 在 Throne 中点击 `Copy for scripts`。
2. 只把密钥本身（去掉 `THRONE_KEY=`）保存到一个文件中，例如 `~/.config/throne/api-key`。在 Linux 和 macOS 上，对该文件运行 `chmod 600`。
3. 设置变量，例如在 `~/.bashrc` 中：

   ```bash
   export THRONE_ADDR=127.0.0.1:9095
   export THRONE_KEY_FILE=~/.config/throne/api-key
   ```

   在 PowerShell 中（`$PROFILE`）：

   ```powershell
   $env:THRONE_ADDR = "127.0.0.1:9095"
   $env:THRONE_KEY_FILE = "$HOME\throne-api-key.txt"
   ```

4. 用 `thronectl status` 测试连接。

参数可以放在命令之前或之后：`--addr host:port` 和 `--key-file file` 优先于环境变量，`--json` 以 JSON 而不是表格形式打印，`--timeout 30s` 设置等待时间（`0` 表示一直等到 Throne 完成）。在 systemd 中，`thronectl` 还会从凭据 `api.key`（`LoadCredential=api.key:/path/to/key`）读取密钥。对于无界面 Throne，请参见[将 thronectl 连接到 Throne](@/headless/thronectl.zh.md#connect)。

## 命令 {#commands}

`thronectl help <command>` 会说明某条命令。各条命令使用列表中显示的 ID。

| 命令 | 作用 |
| --- | --- |
| `status` | 状态、正在运行的配置档、各种模式、路由配置档、Throne 的版本；如果有其他程序占用了 TUN，还会显示它是什么。 |
| `groups` | 列出分组及其自己的路由配置档。 |
| `profiles [--group N] [--name REGEX] [--ids 1,2]` | 列出配置档及其最近的测试结果。 |
| `connect <id>` | 启动一个配置档。`--restart` 会在它已经运行时重新启动它。 |
| `connect last` | 启动正在运行的配置档，否则启动上次的配置档，否则启动记住的配置档。 |
| `connect best [--group N] [--name REGEX] [--max-age 1h]` | 启动延迟最低的配置档，并先测试那些没有比 `--max-age` 更新的结果的配置档。 |
| `disconnect`、`toggle` | 停止正在运行的配置档；没有配置档运行时，`toggle` 执行 `connect last`。 |
| `tun on`、`tun off`、`tun toggle` | TUN 模式。 |
| `proxy on`、`proxy off`、`proxy toggle` | 系统代理。 |
| `routes`、`route <id>` | 列出路由配置档；将某个路由配置档设为活动配置档，并重新启动正在运行的配置档。 |
| `test latency`、`test ip`、`test speed` | 对 `--group N`（可选再加 `--name REGEX`）或配置档 ID 运行 URL 测试、IP 测试或速度测试。 |
| `jobs`、`job <id>`、`cancel <id>` | 列出最近的测试、显示某个测试、停止某个测试。 |
| `clear` | 清除测试结果，选择方式与 `test` 相同。 |
| `live`、`traffic [--by profile] [--by app] [--since 24h]` | 当前的流量速率；流量总计。`--since` 也接受天数，例如 `7d`。 |
| `logs [-n N] [-f]` | 日志的最后 `N` 行（默认 200 行，最多 1000 行）。`-f` 会持续打印新行。 |
| `import [--group N] FILE` | 导入不超过 900 KiB 的分享链接、订阅，或 Clash、sing-box、Xray、WireGuard 或 OpenVPN 配置。`-` 表示从标准输入读取；`--url URL` 让 Throne 下载某个 URL 一次。 |
| `sub add <url> [--name NAME]` | 添加一个订阅分组并更新它。选项：`--no-auto-update`、`--no-update`、`--route ID`（分组自己的路由配置档）、`--import-route`（保留订阅提供的路由配置档）。 |
| `sub update <id>…`、`sub update --all` | 更新订阅分组，或更新所有未归档的分组。 |
| `group add <name>`、`group rm <id>` | 添加一个分组；删除一个分组及其中的配置档。 |
| `profile rm <id>…`、`profile rm --group N [--name REGEX]` | 删除配置档。 |
| `settings [get] [KEY…]`、`settings set KEY=VALUE…` | 显示或更改[设置](#settings)。 |
| `backup create [-o FILE] [--parts LIST]` | 写入一个 `.thrbackup` 存档。 |
| `backup restore FILE [--parts LIST] [--yes]` | 显示存档的内容，询问后恢复它，并重启 Throne。默认部分：`profiles,routes,otp,ip_lists`。 |
| `rulesets update` | 重新下载正在运行的配置档所用的远程规则集。 |
| `geo`、`geo update [geoip] [geosite] [--only-missing]` | 显示 Xray 的 geo 文件；重新下载它们。 |
| `restart`、`shutdown [--yes]` | 重启 Throne；询问后退出 Throne。 |
| `web [--listen ADDR] …`、`web passwd` | 提供 [Web 界面](@/headless/web.zh.md)；设置它的密码。 |
| `keygen [-o FILE]` | 创建一个新密钥，打印它或把它写入一个只有你能读取的新文件。它不会连接 Throne。 |
| `call <METHOD> <PATH> [key=value…] [--body JSON]` | 发送 [API 规范](https://github.com/throneproj/Throne-api/blob/main/spec/api.md)中的任意请求。 |
| `completion bash`、`zsh`、`fish` 或 `powershell` | 打印一个[补全](#completion)脚本。 |
| `version` | 打印 `thronectl` 的版本。 |

- 不加 `--group` 时，`connect best` 和 `import` 使用 Throne 当前显示的分组。
- `--name` 是一个不区分大小写的正则表达式，可以匹配名称中的任意部分。请给它加上引号：`--name 'DE|NL'`。
- `--parts` 接受一个用逗号分隔的列表，可包含 `profiles`、`routes`、`settings`、`otp`、`ip_lists` 和 `icons`。
- 命令会等待结果，最长等待 `--timeout`；如果 Throne 需要更长时间，它会自行完成操作。`--no-wait` 会立即返回。请给测试和 geo 下载更多时间，例如 `--timeout 15m`。Throne 同一时间只运行一个测试。
- `route` 不会更改那些有[自己的路由配置档](@/guides/routing.zh.md#group-routing)的分组。通过 API，分组只能在 `sub add` 创建它时获得自己的路由配置档；之后要更改，请使用 Throne 中的分组选项。
- 退出码：`0` 表示成功；`1` 表示 Throne 返回错误（`error: <code>: <message>`）、更新失败或对询问回答了否；`2` 表示命令或参数错误；`3` 表示无法连接、密钥被拒绝或无法读取文件。

### 设置 {#settings}

`thronectl settings set log_level=debug kill_switch=true` 用于更改设置。`true`、`false` 和数字按原样发送，其他值按文本发送。只要有一个值错误，Throne 就什么都不更改。

| 范围 | 设置 |
| --- | --- |
| 启动 | `remember_enable` |
| 日志 | `log_level`、`xray_log_level` |
| 混合入站 | `inbound_enabled`、`inbound_address`、`inbound_port` |
| 测试 | `test_url`、`test_concurrency`、`test_timeout_ms` |
| 自动更新 | `sub_auto_update`、`sub_respect_server_interval`、`route_auto_update`（单位为分钟，`0` 表示关闭） |
| 断网保护 | `kill_switch` |
| TUN | `tun_strict_route`、`tun_auto_redirect`（Linux）、`tun_mtu`、`tun_ipv6`、`tun_ipv4_cidr`、`tun_ipv6_cidr` |
| 端口与 API | `dns_in_port`、`dns_in_lan`（[供其他设备使用的 DNS 服务器](@/guides/dns.zh.md#dns-server)）、`clash_api_port`、`clash_listen_addr`、`singbox_api_port` |
| DNS 与时间 | `remote_dns`、`direct_dns`、`ntp_enabled`、`ntp_server`、`ntp_interval` |

大多数更改会在正在运行的配置档重新启动时生效（TUN 设置在下次启动 TUN 模式时生效）。需要重新启动时，`thronectl status` 会提示，`thronectl connect last --restart` 会执行重新启动。允许的值见规范中的[设置表](https://github.com/throneproj/Throne-api/blob/main/spec/api.md#settings)。

## 管理请求 {#admin-requests}

`logs`、`import`、`sub add`、`group add`、`group rm`、`profile rm`、`settings set`、`backup`、`restart` 和 `shutdown` 发送的是管理请求（`logs` 是因为日志会列出你访问过的网站）。其他命令使用同一个密钥发送控制请求。

Throne 只响应来自其所在电脑（`127.0.0.1` 或 `::1`）的管理请求。从其他设备发出时，这些请求会以 `forbidden` 失败，除非开启了 `允许从局域网进行管理`（无界面 Throne 则使用 `--api-admin-lan`）。

- 备份存档以明文形式包含你的配置档的密码和密钥，以及你的 OTP 密钥。`thronectl` 创建的存档只有你能读取；请保持这种状态，并在不再需要时删除它们。
- 如果存档中的配置档会运行程序或读取文件（例如额外核心（Extra Core）配置档），`backup restore` 会拒绝该存档（`invalid_archive`）。请在 Throne 窗口中恢复这类存档。
- `backup restore` 和 `shutdown` 会先询问。在没有终端的脚本中，请加上 `--yes`。
- 恢复 `settings` 时，会保留属于这台电脑的设置，例如 Throne API、端口、TUN 和断网保护。

## 示例 {#examples}

```bash
thronectl profiles --group 3 --name 'DE|NL'
thronectl connect 12
thronectl --timeout 2m test latency --group 3
thronectl sub add 'https://sub.example.com/abc' --name Provider
thronectl connect best --max-age 10m   # retest results older than 10 minutes
```

对分组 3 中名称包含 `DE` 或 `NL` 的配置档运行速度测试，然后连接最快的那个（需要 [jq](https://jqlang.org/)）：

```bash
best=$(thronectl --json --timeout 15m test speed --group 3 --name 'DE|NL' |
  jq -r '[.results[] | select(.download_bps > 0)] | max_by(.download_bps) | .id // empty')
[ -n "$best" ] && thronectl connect "$best"
```

在 PowerShell 中：

```powershell
$job = thronectl --json --timeout 15m test speed --group 3 --name 'DE|NL' | ConvertFrom-Json
$best = $job.results | Where-Object download_bps -gt 0 | Sort-Object download_bps -Descending | Select-Object -First 1
if ($best) { thronectl connect $best.id }
```

### 用一个按键连接和断开 {#example-hotkey}

让 Stream Deck、宏键盘或热键工具上的按键运行 `thronectl toggle`。这类工具通常不会读取你的 shell 设置：请把 `THRONE_KEY_FILE`（如有需要，还有 `THRONE_ADDR`）设为用户环境变量，或者加上 `--key-file`。Throne 也为 `Start/Stop Profile` 和 `Toggle Tun Mode` 提供了[全局热键](@/reference/shortcuts.zh.md#global-hotkeys)。

## Shell 补全 {#completion}

`thronectl completion <shell>` 会打印一个脚本，用于补全命令、参数和设置名称；如果 Throne 在一秒内响应，还会补全配置档、分组、路由配置档和测试的 ID。

```bash
# bash: in ~/.bashrc (on macOS, ~/.bash_profile)
eval "$(thronectl completion bash)"
# zsh: in ~/.zshrc
autoload -Uz compinit && compinit
source <(thronectl completion zsh)
# fish
thronectl completion fish > ~/.config/fish/completions/thronectl.fish
```

```powershell
# In $PROFILE
thronectl completion powershell | Out-String | Invoke-Expression
```

## 安全 {#security}

- 持有密钥的人可以控制 Throne 的连接、模式和路由，并可以读取你的配置档的名称、地址、测试结果和流量。在允许[管理请求](#admin-requests)的地方，密钥还能读取配置档的配置和备份，它们以明文形式包含你的凭据。
- 不要把密钥写在别人能读取的命令行和脚本中：请使用只有你能读取的密钥文件，或者使用来自密钥存储的 `THRONE_KEY`。
- `thronectl` 与 Throne 之间的流量使用密钥加密和认证，但不提供前向保密：获得密钥的人可以读取之前录下的流量。
- 如果密钥泄露，请点击 `Regenerate`，并把新密钥交给你的脚本。对于无界面 Throne，请参见[防锁定与恢复](@/headless/recovery.zh.md)。
- 除非你需要在网络上使用 API，否则请让 `Listen on` 保持为 `This computer only`。
- 如果一分钟内连接失败 5 次，Throne 会在 60 秒内拒绝该地址，同一路由器之后的其他客户端也会被锁定。Throne 从不拒绝 `127.0.0.1`，也不会拒绝在过去 10 分钟内有客户端成功登录过的地址。
- Throne 会把每个会更改内容的请求连同客户端地址写入日志。

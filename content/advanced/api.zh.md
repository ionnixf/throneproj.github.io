+++
title = "Throne API 与 thronectl"
description = "通过 Throne API 及其命令行客户端 thronectl，从脚本、热键和其他设备控制和管理 Throne。"
weight = 80
toc = true
+++

Throne API（自 1.4.0 起）让脚本、热键工具和其他设备可以控制 Throne：启动和停止配置档、切换 TUN 模式和系统代理、更改路由配置档、运行测试，以及读取配置档和流量统计。它还可以管理 Throne：导入配置档、添加和更新订阅、删除分组和配置档、更改设置、备份和恢复，以及重启或退出 Throne。

`thronectl` 是该 API 的命令行客户端；`thronectl web` 还为浏览器提供一个 [Web 界面](@/headless/web.zh.md)。本页介绍如何在 Throne 桌面版中开启 API、如何安装并连接 `thronectl`，以及每条命令的作用。

[无界面 Throne](@/headless/_index.zh.md) 在服务器或容器中以无窗口方式运行，它从首次启动起就开启了 API，你需要用 `thronectl` 来管理它。[使用 thronectl 管理](@/headless/thronectl.zh.md)介绍了如何连接并设置它。

Throne for Android 没有 Throne API。

## 在 Throne 中开启 API {#enable}

API 默认关闭。

1. 打开 `设置`（Settings）→ `Integration Settings`（仅限高级模式，参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)），然后打开 `Throne API` 标签页。
2. 勾选 `Enable the Throne API`。Throne 会填入一个新的密钥。
3. 点击 `确定`（OK）。`Status` 会显示 `Listening on 127.0.0.1:9095`。

该标签页包含以下控件：

| 控件 | 作用 |
| --- | --- |
| `Enable the Throne API` | 开启 API。 |
| `Listen on` | `This computer only`（默认）监听 `127.0.0.1`，因此只有这台电脑上的程序可以连接。`Local network` 监听这台电脑的所有地址，因此其他设备也可以连接。 |
| `Port` | TCP 端口，默认为 `9095`。 |
| `Allowed addresses` | 仅适用于 `Local network`：允许连接的 IP 地址和地址范围，例如 `192.168.1.0/24, 10.0.0.5`，用逗号或空格分隔。留空则允许所有地址连接。这台电脑上的程序始终可以通过 `127.0.0.1` 连接。 |
| `允许从局域网进行管理`（Allow management from the local network） | 仅适用于 `Local network`：其他设备也可以发送[管理请求](#admin-requests)，例如导入、更改设置和备份。这台电脑上的程序始终可以发送这类请求。 |
| `Key` | 客户端所需的密钥，长度为 43 个字符。`Show` 显示密钥，`Copy` 复制密钥，`Regenerate` 生成新密钥。 |
| `Copy for scripts` | 复制 `thronectl` 读取的两项设置：`THRONE_ADDR` 和 `THRONE_KEY`。使用 `Local network` 时，地址为这台电脑在你网络中的地址。 |
| `Status` | Throne 是否正在监听，例如 `Listening on port 9095 (local network)`；或者没有监听的原因，例如 `Port 9095 is in use`。 |

- 更改在你点击 `确定`（OK）后生效。新的密钥、端口或地址会关闭已打开的连接。使用旧密钥的脚本会停止工作，因此在 `Regenerate` 替换已保存的密钥之前，Throne 会先询问你。
- 使用 `Local network` 时，窗口会警告你：你网络中的设备可以凭密钥控制 Throne。你的防火墙可能会询问是否允许 Throne。
- Throne 会把每个会更改内容的请求连同客户端地址写入日志，例如 `[API] PUT /v1/connection from 127.0.0.1`。以 `[API]` 开头的行也会显示被拒绝的连接。
- 如果开启了[断网保护（kill switch）](@/advanced/kill_switch.zh.md)，并且 API 在局域网上监听，断网保护会放行 API 的响应，因此客户端仍然可以连接。

## 安装 thronectl {#install-thronectl}

`thronectl` 是一个适用于 Linux、Windows、macOS 和 FreeBSD 的单一程序。Throne 的 `.deb` 和 `.rpm` 软件包以及 Linux ZIP 包都包含它，无界面 Throne 的 Docker 镜像中也包含它。Throne 的 Windows 和 macOS 下载包中没有它。

- 从 [Throne-api 的发布页面](https://github.com/throneproj/Throne-api/releases)下载并解压，然后把 `thronectl`（在 Windows 上为 `thronectl.exe`）放到 `PATH` 中的某个文件夹里。
- 或者，如果已安装 Go：

  ```bash
  go install github.com/throneproj/Throne-api/cmd/thronectl@latest
  ```

`thronectl version` 会打印它的版本。

## 将 thronectl 连接到 Throne {#connect}

`thronectl` 从环境变量中读取 Throne 的地址和密钥：

| 变量 | 内容 |
| --- | --- |
| `THRONE_ADDR` | 地址和端口，例如 `127.0.0.1:9095`。默认值：`127.0.0.1:9095`。 |
| `THRONE_KEY` | 密钥本身。 |
| `THRONE_KEY_FILE` | 保存密钥的文件的路径。 |

`thronectl` 从不接受以命令行参数形式给出的密钥，因此密钥不会出现在你的 shell 历史记录中。请把密钥保存在只有你能读取的文件中，并让 `THRONE_KEY_FILE` 指向该文件：

1. 在 Throne 中点击 `Copy for scripts`。
2. 把 `THRONE_KEY=…` 这一行去掉 `THRONE_KEY=`，也就是只把密钥本身保存到一个文件中，例如 `~/.config/throne/api-key`。在 Linux 和 macOS 上，对该文件运行 `chmod 600`。
3. 设置变量，例如在 `~/.bashrc` 中：

   ```bash
   export THRONE_ADDR=127.0.0.1:9095
   export THRONE_KEY_FILE=~/.config/throne/api-key
   ```

   在 PowerShell 中，例如在 `$PROFILE` 中：

   ```powershell
   $env:THRONE_ADDR = "127.0.0.1:9095"
   $env:THRONE_KEY_FILE = "$HOME\throne-api-key.txt"
   ```

4. 测试连接：

   ```bash
   thronectl status
   ```

以下参数可以放在命令之前或之后：

| 参数 | 作用 |
| --- | --- |
| `--addr host:port` | Throne 的地址。默认值：`THRONE_ADDR`，否则为 `127.0.0.1:9095`。 |
| `--key-file file` | 从该文件读取密钥。它优先于 `THRONE_KEY` 和 `THRONE_KEY_FILE`。 |
| `--json` | 以 JSON 而不是表格的形式打印 Throne 的响应。 |
| `--timeout 30s` | 超过这段时间后放弃，默认为 `30s`。`0` 表示一直等到 Throne 完成。 |

在 systemd 单元中，如果以上方式都没有提供密钥，`thronectl` 还会从凭据 `api.key`（`LoadCredential=api.key:/path/to/key`）读取密钥，[Web 界面](@/headless/web.zh.md#systemd)的 `throne-web.service` 就是这样做的。

对于无界面 Throne，`Throne --print-connection` 会打印这两个变量的 `export` 行。参见[将 thronectl 连接到 Throne](@/headless/thronectl.zh.md#connect)。

## 命令 {#commands}

`thronectl help <command>` 会说明某条命令。`thronectl` 在它的列表中显示配置档、分组、路由配置档和测试的 ID；各条命令使用这些 ID。

| 命令 | 作用 |
| --- | --- |
| `status` | 显示状态、正在运行的配置档、各种模式、正在使用的路由配置档、Throne 的版本；如果有其他程序占用了 TUN，还会显示它是什么。 |
| `groups` | 列出分组及其 ID。`ROUTE` 显示分组自己的路由配置档；分组跟随活动的路由配置档时显示 `active`。 |
| `profiles [--group N] [--name REGEX] [--ids 1,2]` | 列出配置档及其最近的测试结果。 |
| `connect <id>` | 启动一个配置档。`--restart` 会在它已经运行时重新启动它。 |
| `connect last` | 启动正在运行的配置档，否则启动上次的配置档，否则启动记住的配置档。 |
| `connect best [--group N] [--name REGEX] [--max-age 1h]` | 启动延迟最低的配置档。它会先测试那些没有比 `--max-age` 更新的结果的配置档。默认分组为 Throne 当前显示的分组。 |
| `disconnect` | 停止正在运行的配置档。 |
| `toggle` | 没有配置档运行时执行 `connect last`，否则执行 `disconnect`。 |
| `tun on`、`tun off`、`tun toggle` | 开启或关闭 TUN 模式。 |
| `proxy on`、`proxy off`、`proxy toggle` | 开启或关闭系统代理。 |
| `routes` | 列出路由配置档。`*` 标记活动的路由配置档。另有备注标记正在运行的配置档所在分组使用的路由配置档，以及由订阅分组更新的路由配置档。 |
| `route <id>` | 将某个路由配置档设为活动配置档，并重新启动正在运行的配置档，除非正在运行的配置档所在的分组使用自己的路由配置档。 |
| `test latency`、`test ip`、`test speed` | 运行 URL 测试、IP 测试或速度测试，并显示结果。请加上 `--group N`（可选再加 `--name REGEX`）或配置档 ID。 |
| `jobs`、`job <id>`、`cancel <id>` | 列出最近的测试、显示某个测试、停止某个测试。 |
| `clear` | 清除测试结果。请加上 `--group N`（可选再加 `--name REGEX`）或配置档 ID。 |
| `live` | 显示当前的流量速率。 |
| `traffic [--by profile] [--by app] [--since 24h]` | 按配置档（默认）或按应用显示流量总计。`--since` 也接受天数，例如 `7d`。 |
| `logs [-n N] [-f]` | 打印 Throne 日志的最后 `N` 行，默认 200 行，最多 1000 行。`-f` 随后会持续打印新行，直到你停止它，连接中断或 Throne 重启时也会继续。使用 `--json` 时，每行输出一个 JSON 对象。 |
| `import [--group N] FILE` | 从一个不超过 900 KiB 的文件导入分享链接、订阅内容，或 Clash、sing-box、Xray、WireGuard 或 OpenVPN 配置。`-` 表示从标准输入读取；`--url URL` 让 Throne 下载某个 URL 一次。默认分组为 Throne 当前显示的分组。 |
| `sub add <url> [--name NAME]` | 添加一个订阅分组并更新它，默认以 URL 的主机名命名。`--no-auto-update` 让它不参与自动更新；`--no-update` 表示现在不下载。`--route ID` 为该分组指定自己的路由配置档；`--import-route` 保留订阅提供的路由配置档。 |
| `sub update <id>…`、`sub update --all` | 更新订阅分组，或更新所有未归档的分组。 |
| `group add <name>`、`group rm <id>` | 添加一个分组；删除一个分组及其中的配置档。 |
| `profile rm <id>…`、`profile rm --group N [--name REGEX]` | 删除配置档。 |
| `settings [get] [KEY…]` | 显示由 API 管理的设置。参见[设置](#settings)。 |
| `settings set KEY=VALUE…` | 更改设置。`true`、`false` 和数字按原样发送，其他值按文本发送。 |
| `backup create [-o FILE] [--parts LIST]` | 写入一个 `.thrbackup` 存档，默认为 `throne-backup-<time>.thrbackup`。 |
| `backup restore FILE [--parts LIST] [--yes]` | 显示存档的内容，询问后恢复它，并重启 Throne。默认部分：`profiles,routes,otp,ip_lists`。 |
| `rulesets update` | 重新下载正在运行的配置档所用的远程规则集。 |
| `geo`、`geo update [geoip] [geosite] [--only-missing]` | 显示 Xray 的 geo 文件；重新下载它们。 |
| `restart` | 重启 Throne。 |
| `shutdown [--yes]` | 询问后退出 Throne。 |
| `web [--listen ADDR] …`、`web passwd` | 提供用于在浏览器中管理 Throne 的 [Web 界面](@/headless/web.zh.md)，直到你停止它；设置它的密码。 |
| `keygen [-o FILE]` | 创建一个新密钥：打印它，或把它写入一个只有你能读取的新文件。它不会连接 Throne。 |
| `call <METHOD> <PATH> [key=value…] [--body JSON]` | 发送 [API 规范](https://github.com/throneproj/Throne-api/blob/main/spec/api.md)中的任意请求，并打印响应。 |
| `completion bash`、`zsh`、`fish` 或 `powershell` | 打印一个 [Shell 补全](#completion)脚本。 |
| `version` | 打印 `thronectl` 的版本。 |

需要了解的几点：

- `--name` 是一个不区分大小写的正则表达式，可以匹配名称中的任意部分。请在 shell 中给它加上引号，例如 `--name 'DE|NL'`。
- `--parts` 接受一个用逗号分隔的列表，可包含 `profiles`、`routes`、`settings`、`otp`、`ip_lists` 和 `icons`。
- 启动或停止配置档的命令会等待结果，最长等待 `--timeout`。如果 Throne 需要更长时间，`thronectl` 会显示当前状态（例如 `connecting`），Throne 会自行完成操作。导入、更新、删除和下载也以同样的方式等待。`--no-wait` 会立即返回。
- 测试和 geo 下载可能需要几分钟，因此请给它们更长的 `--timeout`，例如 `--timeout 15m`。耗时更长的测试会在 Throne 中继续运行；可以用 `thronectl job <id>` 跟踪它。
- Throne 同一时间只运行一个测试，无论你是从窗口还是通过 API 启动它。
- 分组可以使用自己的路由配置档进行连接（参见[按分组设置路由配置档](@/guides/routing.zh.md#group-routing)）。此时 `route` 只会更改其他分组的路由配置档。通过 API，只有在 `sub add` 创建分组时才能为它指定自己的路由配置档；之后要更改，请使用 Throne 窗口中的分组选项。
- `thronectl` 成功时以 `0` 退出；以下情况以 `1` 退出：Throne 返回错误（打印为 `error: <code>: <message>`）、订阅、规则集或 geo 更新失败，或者对询问回答了否；命令或参数错误时以 `2` 退出；无法连接、密钥被拒绝或无法读取文件时以 `3` 退出。

### 设置 {#settings}

`thronectl settings` 显示 API 可以更改的设置，`thronectl settings set` 用于更改它们，例如 `thronectl settings set log_level=debug kill_switch=true`。Throne 会先检查每个值，只要有一个值错误，就什么都不更改。

| 范围 | 设置 |
| --- | --- |
| 启动 | `remember_enable` |
| 日志 | `log_level`、`xray_log_level` |
| 混合入站 | `inbound_enabled`、`inbound_address`、`inbound_port` |
| 测试 | `test_url`、`test_concurrency`、`test_timeout_ms` |
| 自动更新 | `sub_auto_update`、`sub_respect_server_interval`、`route_auto_update`（单位为分钟，`0` 表示关闭） |
| 断网保护 | `kill_switch` |
| TUN | `tun_strict_route`、`tun_auto_redirect`（Linux）、`tun_mtu`、`tun_ipv6`、`tun_ipv4_cidr`、`tun_ipv6_cidr` |
| 端口与 API | `dns_in_port`、`dns_in_lan`、`clash_api_port`、`clash_listen_addr`、`singbox_api_port` |
| DNS 与时间 | `remote_dns`、`direct_dns`、`ntp_enabled`、`ntp_server`、`ntp_interval` |

`dns_in_lan` 让你网络中的其他设备可以通过 `dns_in_port` 使用核心的 DNS 服务器；参见[供其他设备使用的 DNS 服务器](@/guides/dns.zh.md#dns-server)。

对日志、入站、端口、DNS 和 NTP 的大多数更改会在正在运行的配置档重新启动时生效。只有当配置档正以 TUN 模式运行时，TUN 设置才需要这次重新启动；否则它们会在下次启动 TUN 模式时生效。需要重新启动时，`thronectl status` 会提示；`thronectl connect last --restart` 会应用这些更改。规范中的[设置表](https://github.com/throneproj/Throne-api/blob/main/spec/api.md#settings)列出了允许的值。

## 管理请求 {#admin-requests}

以下命令发送的是管理请求：`logs`、`import`、`sub add`、`group add`、`group rm`、`profile rm`、`settings set`、`backup`、`restart` 和 `shutdown`。`logs`（`GET /v1/logs`）之所以属于管理请求，是因为日志会列出连接访问过的网站。其他命令，包括 `sub update`、`rulesets update` 和 `geo update`，发送的是控制请求。两类请求使用同一个密钥。

- Throne 只响应来自其所在电脑的管理请求，即来自 `127.0.0.1` 或 `::1` 的请求。
- 从其他设备发出时，这些请求会以 `forbidden` 失败，除非开启了 `允许从局域网进行管理`（Allow management from the local network）。无界面 Throne 则需要改用 `--api-admin-lan`。
- 备份存档以明文形式包含你的配置档的密码和密钥，以及你的 OTP 密钥。`thronectl` 创建的存档只有你能读取。请保持这种状态，并在不再需要时删除它们。
- 如果存档中的配置档会在电脑上运行程序或读取文件（例如额外核心（Extra Core）配置档），`backup restore` 会拒绝该存档（`invalid_archive`）。请在 Throne 窗口中恢复这类存档。
- `backup restore` 和 `shutdown` 会在执行前询问。在没有终端的脚本中，请加上 `--yes`。

恢复 `settings` 时，会保留属于这台电脑的设置：Throne API、入站和端口、TUN、断网保护、`记住上次的配置档`（Remember last profile）、NTP、日志、系统代理以及各种路径。

## 示例 {#examples}

日常命令：

```bash
thronectl status
thronectl profiles --group 3 --name 'DE|NL'
thronectl connect 12
thronectl tun on
thronectl route 2
thronectl --timeout 2m test latency --group 3
thronectl traffic --by app --since 7d
thronectl sub add 'https://sub.example.com/abc' --name Provider
thronectl --timeout 2m sub update --all
thronectl backup create -o throne.thrbackup
```

### 连接最佳配置档 {#example-best}

连接 Throne 当前显示的分组中延迟最低的配置档。超过十分钟的结果会重新测试：

```bash
thronectl connect best --max-age 10m
```

### 连接最快的配置档 {#example-speed}

对分组 3 中名称包含 `DE` 或 `NL` 的配置档运行速度测试，然后连接最快的那个。这需要 [jq](https://jqlang.org/)：

```bash
best=$(thronectl --json --timeout 15m test speed --group 3 --name 'DE|NL' |
  jq -r '[.results[] | select(.download_bps > 0)] | max_by(.download_bps) | .id // empty')
[ -n "$best" ] && thronectl connect "$best"
```

### 用一个按键连接和断开 {#example-hotkey}

要用 Stream Deck、宏键盘或热键工具上的一个按键连接和断开，请让该按键运行：

```bash
thronectl toggle
```

通过按键启动命令的程序通常不会读取你的 shell 设置。请把 `THRONE_KEY_FILE`（如有需要，还有 `THRONE_ADDR`）设为你的用户账户的环境变量，或者在命令中加上 `--key-file`。Throne 桌面版也为 `Start/Stop Profile` 和 `Toggle Tun Mode` 提供了[全局热键](@/reference/shortcuts.zh.md#global-hotkeys)。

### PowerShell {#example-powershell}

用 PowerShell 实现的速度测试示例：

```powershell
$job = thronectl --json --timeout 15m test speed --group 3 --name 'DE|NL' | ConvertFrom-Json
$best = $job.results | Where-Object download_bps -gt 0 | Sort-Object download_bps -Descending | Select-Object -First 1
if ($best) { thronectl connect $best.id }
```

## Shell 补全 {#completion}

`thronectl completion <shell>` 会打印一个脚本，在 bash、zsh、fish 和 PowerShell 中补全命令、参数、设置名称、备份部分以及其他固定值。如果 Throne 在一秒内响应，它还会补全配置档、分组、路由配置档和测试的 ID，并附上它们的名称。

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

这些脚本会询问 `thronectl` 应补全什么，因此在你更新 `thronectl` 后它们仍然正确。

## 安全 {#security}

- 持有密钥的人可以控制 Throne 的连接、模式和路由，并可以读取你的配置档的名称、地址、测试结果和流量。从这台电脑发出时，或者在开启了 `允许从局域网进行管理`（Allow management from the local network）的情况下从局域网发出时，密钥还允许导入、删除、更改设置和备份，而备份以明文形式包含你的凭据。
- 不要把密钥写在别人能读取的命令行和脚本中。请使用只有你能读取的密钥文件，或者使用来自密码管理器或密钥存储的 `THRONE_KEY`。
- `thronectl` 与 Throne 之间的每条消息都使用密钥加密和认证，在局域网上也是如此。它不提供前向保密：获得密钥的人可以读取之前录下的流量。
- 如果密钥泄露，请点击 `Regenerate`，并把新密钥交给你的脚本。对于无界面 Throne，请参见[防锁定与恢复](@/headless/recovery.zh.md)。
- 除非你需要在网络上使用 API，否则请让 `Listen on` 保持为 `This computer only`。在网络上使用时，请在 `Allowed addresses` 中列出允许连接的设备。
- 如果同一地址在一分钟内连接失败 5 次，Throne 会在 60 秒内拒绝该地址。在此期间，同一地址的其他客户端（例如位于同一路由器之后的设备）也会被锁定。Throne 从不拒绝 `127.0.0.1`，也不会拒绝在过去 10 分钟内有客户端成功登录过的地址。
- 响应中从不包含你的配置档的凭据或你的订阅的 URL。备份是例外。

## Go 库 {#go-library}

[Throne-api](https://github.com/throneproj/Throne-api) 仓库还包含一个 Go 库 `github.com/throneproj/Throne-api`，它为每种请求都提供了一个方法；仓库中还有包含通信协议的 [API 规范](https://github.com/throneproj/Throne-api/blob/main/spec/api.md)，因此你可以用其他语言编写客户端。

跟踪 Throne 的客户端不需要轮询。`GET /v1/events` 会等到有内容变化，并说明变化的是什么，例如状态、分组、配置档、测试或设置，这样客户端只需重新读取这些内容。`GET /v1/logs` 返回客户端已有的最后几行之后的新日志行，也可以等待新行出现。`thronectl logs -f` 和 [Web 界面](@/headless/web.zh.md)都使用这两个请求。

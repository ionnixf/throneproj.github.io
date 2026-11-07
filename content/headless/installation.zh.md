+++
title = "安装与服务"
description = "将无界面 Throne 安装为 systemd 服务、systemd 用户单元、Docker 容器、macOS LaunchDaemon 或 Windows 服务。"
weight = 10
toc = true
+++

请按照[安装](@/get_started/installation.zh.md)中的说明安装 Throne，然后让服务管理器运行它：

| 方式 | 运行身份 | 数据目录 |
| --- | --- | --- |
| [Linux 软件包（systemd 单元）](#linux-packages) | root | `/var/lib/throne` |
| [systemd 用户单元](#user-unit)，任意 Linux | 你自己 | `~/.config/Throne` |
| [Docker](#docker) | 容器中的 root | 卷 `/var/lib/throne` |
| [macOS LaunchDaemon](#macos) | root | `/Library/Application Support/Throne` |
| [Windows 服务](#windows) | LocalSystem | `C:\ProgramData\Throne` |

然后[连接 thronectl](@/headless/thronectl.zh.md#connect)。

## Debian、Ubuntu、Raspberry Pi OS、Fedora、openSUSE {#linux-packages}

来自[发布页面](https://github.com/throneproj/Throne/releases)和软件源的 `.deb` 与 `.rpm` 软件包会把 Throne 安装到 `/opt/Throne`，并安装 systemd 单元 `throne.service`、它的环境文件 `/etc/throne/throne.env` 以及 `thronectl`。哪个软件包适合哪个系统，请参见 [Linux](@/get_started/installation.zh.md#linux)；在树莓派上，请使用 64 位的 Raspberry Pi OS。

```bash
sudo apt install ./Throne-<version>-debian-amd64.deb   # 或：sudo dnf install ./Throne-<version>-fedora-amd64.rpm
sudo /opt/Throne/Throne --check                        # 可选：这台电脑能否运行它？
sudo systemctl enable --now throne
journalctl -u throne -f
```

- 软件包从不自行启用该单元。升级时，如果该单元正在运行，会重启它；卸载软件包时会停止并禁用它。
- 卸载软件包后，`/var/lib/throne`（配置档、设置和 API 密钥）仍会保留，即使使用 `apt purge` 也是如此。
- 要设置某个变量，请在 `/etc/throne/throne.env` 中取消它的注释，然后运行 `sudo systemctl restart throne`。升级会保留你的修改。切勿把 API 密钥放进这个文件；请改用 `THRONE_API_KEY_FILE` 指定一个密钥文件。
- 要更改命令行，请用 `sudo systemctl edit throne` 添加一个 drop-in 文件：

  ```ini
  [Service]
  ExecStart=
  ExecStart=/opt/Throne/Throne --headless --data-dir=%S/throne --debug
  ```

- 第二个单元 `throne-web.service` 运行 [Web 界面](@/headless/web.zh.md#systemd)。它同样不会被启用。

## 其他 Linux 安装方式 {#other-linux}

[安装脚本](@/get_started/installation.zh.md#linux-script)和[便携版 ZIP](@/get_started/installation.zh.md#linux-zip) 都不安装单元，[AUR 软件包](@/get_started/installation.zh.md#aur)也可能没有附带单元。请安装软件包中的单元：

```bash
curl -fsSLo throne.service https://raw.githubusercontent.com/throneproj/Throne/dev/dist/linux/throne.service
sudo install -m 644 throne.service /etc/systemd/system/throne.service
sudo systemctl daemon-reload && sudo systemctl enable --now throne
```

该单元运行 `/opt/Throne/Throne`，也就是安装脚本放置 Throne 的位置。如果 Throne 在其他文件夹中，例如 AUR 软件包的 `/usr/lib/throne`，请用 drop-in 文件（`sudo systemctl edit throne`）修改路径：

```ini
[Service]
ExecStart=
ExecStart=/usr/lib/throne/Throne --headless --data-dir=%S/throne
```

如果想要变量示例，请把 [`dist/linux/throne.env`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne.env) 保存为 `/etc/throne/throne.env`。

## Linux 上的权限 {#linux-privileges}

TUN 和 [Kill Switch](@/advanced/kill_switch.zh.md) 需要权限。[额外核心](@/advanced/chains.zh.md#extra-core)（Extra Core）配置档需要一个普通用户，因为 Throne 从不以 root 身份运行额外核心。请选择该单元的运行方式：

| 单元的运行方式 | TUN | Kill Switch | 额外核心配置档 |
| --- | --- | --- | --- |
| 以 root 身份（单元的原始配置） | 是 | 是 | 否 |
| 以 `throne` 身份并借助能力（`caps.conf`） | 是 | 否（`unsupported`） | 是 |
| 以 `throne` 身份，搭配 setuid root 的 `ThroneCore` | 是 | 是 | 是 |
| 以 `throne` 身份，不做其他设置 | 否 | 否 | 是 |

**能力（capabilities）。** 下载 [`caps.conf`](https://github.com/throneproj/Throne/blob/dev/dist/linux/caps.conf)，然后运行：

```bash
sudo useradd --system --no-create-home --home-dir /var/lib/throne --shell /usr/sbin/nologin throne
sudo install -Dm644 caps.conf /etc/systemd/system/throne.service.d/caps.conf
sudo systemctl daemon-reload && sudo systemctl restart throne
```

在这种配置下，`ThroneCore` 不能带有 setuid 位，也不能带有文件能力，因为运行这样的文件会丢弃该单元授予的能力。

**setuid 核心。** 使用一个在 `[Service]` 下只包含 `User=throne` 和 `Group=throne` 的 drop-in 文件，并让核心以 root 身份运行：

```bash
sudo chown root:root /opt/Throne/ThroneCore && sudo chmod u+s /opt/Throne/ThroneCore
```

软件包升级会用不带 setuid 位的文件替换 `ThroneCore`。在 Debian 和 Ubuntu 上，`sudo dpkg-statoverride --update --add root root 4755 /opt/Throne/ThroneCore` 可以保留该位。不要在该单元中添加 `NoNewPrivileges=`，也不要添加隐含它的加固选项：它会使 setuid 核心无法工作。

`thronectl status` 会在 `capabilities.tun` 和 `capabilities.kill_switch` 中显示这个 Throne 能做什么。

## systemd 用户单元 {#user-unit}

没有 root 权限时，Throne 以用户单元的形式在你自己的会话中运行。下载 [`throne-user.service`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne-user.service)，然后运行：

```bash
install -Dm644 throne-user.service ~/.config/systemd/user/throne.service
systemctl --user daemon-reload && systemctl --user enable --now throne
loginctl enable-linger "$USER"   # 开机时启动，无需登录
```

- 该单元使用桌面版的数据目录 `~/.config/Throne`，拥有相同的配置档和设置。请先退出桌面版；再次打开桌面版之前，请先运行 `systemctl --user stop throne`。
- TUN 和 Kill Switch 需要 setuid root 的核心。桌面版第一次请求[权限](@/guides/tun_mode.zh.md#privileges-linux)时会完成这项设置，`Throne --check` 也会打印所需的命令。
- 以你自己的用户身份运行时，`Throne --health`、`--print-connection` 和 `--check` 无需任何选项就能找到这个 Throne。

## Docker {#docker}

镜像 `ghcr.io/throneproj/throne`（某个发布版标签，或 `latest`）可在 `linux/amd64` 和 `linux/arm64` 上运行，其中的 `thronectl` 位于 `/usr/local/bin`。请从 Throne 代码仓库中的 [`compose.yaml`](https://github.com/throneproj/Throne/blob/dev/docker/compose.yaml) 开始：

```bash
curl -fsSLO https://raw.githubusercontent.com/throneproj/Throne/dev/docker/compose.yaml
docker compose up -d
docker compose exec throne thronectl --key-file /var/lib/throne/config/api.key status
```

它的主要部分：

```yaml
services:
  throne:
    image: ghcr.io/throneproj/throne:latest   # 或某个发布版标签
    restart: unless-stopped
    stop_grace_period: 45s
    cap_add: [NET_ADMIN]                      # TUN 和 Kill Switch
    devices: ["/dev/net/tun:/dev/net/tun"]    # TUN 和 Kill Switch
    volumes: ["throne-data:/var/lib/throne"]
    ports: ["2080:2080"]                      # 混合（HTTP 和 SOCKS）入站
volumes:
  throne-data:
```

- **代理端口。** 混合入站监听 `127.0.0.1`，发布的端口无法访问它：请运行 `thronectl settings set inbound_address=0.0.0.0`，然后重新启动配置档。发布的端口会绕过 `ufw` 等主机防火墙；在可以从互联网访问的主机上，请只发布局域网地址（`"192.168.1.2:2080:2080"`）。
- **主机网络**（`network_mode: host`，没有 `ports:`）：TUN 和 Kill Switch 作用于整个主机，并且主机上只能有一个 Throne 运行 TUN。
- **停止。** 请保留 `stop_grace_period: 45s`，否则 Docker 可能会在 Throne 停止其核心之前就将其杀死。`restart: unless-stopped` 即使在 `thronectl shutdown` 之后也会重新启动 Throne；请使用 `docker compose stop`。
- **健康状态。** 健康检查运行 `Throne --health`。Docker 只会把容器标记为不健康，而不会重启它。
- **限制。** 额外核心配置档无法运行，因为容器以 root 身份运行。在 rootless Docker 和其他使用用户命名空间的容器中，Kill Switch 会以 `unsupported` 失败。
- **选项。** 变量写在 `environment:` 下，参数写在 `command:` 中（追加在 `--headless` 之后）。只能用 `THRONE_DATA_DIR` 更改数据目录，因为健康检查要用到它。
- **thronectl。** 在 `environment:` 下设置 `THRONE_KEY_FILE: /var/lib/throne/config/api.key`，就不再需要 `--key-file`。用 `docker compose exec -T throne thronectl import - < links.txt` 导入文件。
- **从网络访问 API。** 设置 `THRONE_API_LISTEN: "*:9095"` 和 `THRONE_API_ALLOW`，发布端口 9095；导入和更改设置还需要 `THRONE_API_ADMIN_LAN: "1"`，因为经过发布端口的请求都来自容器外部。在 Docker 的 NAT 之后，所有客户端共用同一个地址，因此一个使用旧密钥的客户端会把其他客户端锁在外面一分钟。
- **日志：** `docker compose logs -f throne`。Web 界面请参见 [Docker](@/headless/web.zh.md#docker)。

## macOS {#macos}

LaunchDaemon 会在开机时以 root 身份运行 Throne，支持 TUN 和 Kill Switch，但不支持额外核心配置档。请把 `Throne.app` 移到 `/Applications`，下载 [`moe.Throne.headless.plist`](https://github.com/throneproj/Throne/blob/dev/dist/macos/moe.Throne.headless.plist)，然后运行：

```bash
sudo mkdir -p /Library/Logs/Throne
sudo install -m 644 -o root -g wheel moe.Throne.headless.plist /Library/LaunchDaemons/
sudo xattr -dr com.apple.quarantine /Applications/Throne.app   # 仅限未签名的版本
sudo launchctl bootstrap system /Library/LaunchDaemons/moe.Throne.headless.plist
```

- 停止并卸载它：`sudo launchctl bootout system/moe.Throne.headless`。
- 日志位于 `/Library/Logs/Throne/throne.log`。要查看状态，请运行 `sudo /Applications/Throne.app/Contents/MacOS/Throne --health`。
- 切勿用 `open` 启动无界面 Throne：它会立即返回，launchd 也就无法跟踪该进程。
- 每次以错误退出后，launchd 都会在 10 秒后再次启动 Throne，即使是重新启动也无法解决的错误（退出码 3、64 和 78）也是如此。如果 Throne 不断启动又退出，请阅读日志。

## Windows 服务 {#windows}

`Throne.exe --headless --service` 以 Windows 服务的形式在 LocalSystem 账户下运行，因此 TUN 和 Kill Switch 可以工作。

1. 使用安装程序为所有用户安装 Throne，安装到 `Program Files` 中。服务不能运行普通用户可以替换的程序，因此不要使用按用户安装的方式，也不要使用放在你用户文件夹中的 ZIP。
2. 下载 [`install-service.bat`](https://github.com/throneproj/Throne/blob/dev/dist/windows/install-service.bat) 和 [`uninstall-service.bat`](https://github.com/throneproj/Throne/blob/dev/dist/windows/uninstall-service.bat)。
3. 在以管理员身份运行的命令提示符中运行 `install-service.bat`；如果 Throne 位于其他文件夹，请运行 `install-service.bat "D:\Apps\Throne\Throne.exe"`。

服务 `Throne` 在开机后启动（延迟启动）。它的恢复操作会在 3 秒、10 秒、之后每次 60 秒后重启 Throne，即使 Throne 以错误码退出也是如此，例如重启请求（退出码 75）或看门狗退出。

- `sc.exe stop Throne` 和 `thronectl shutdown` 会让它彻底停止。更新 Throne 之前，请先停止该服务。
- 出错后，`sc.exe query Throne` 会把 Throne 自己的退出码显示为 `SERVICE_EXIT_CODE`（此时 `WIN32_EXIT_CODE` 为 `1066`）。日志位于 `C:\ProgramData\Throne\config\logs\throne.log`。
- `uninstall-service.bat` 会删除该服务，并保留数据。

### 数据文件夹 {#windows-data-folder}

该服务让 `C:\ProgramData\Throne` 只供 SYSTEM 和 Administrators 使用。如果该文件夹属于其他用户、允许其他用户写入（你在 `C:\ProgramData` 下手动创建的文件夹就是这样），或者本身是链接或联接点（junction）、或其中包含这样的项，服务就会以退出码 78 退出，并在日志中指出问题。请删除该文件夹，然后重新启动服务。

### 在命令行窗口中运行命令 {#windows-prompt}

请在以管理员身份运行的命令行窗口中运行 `Throne.exe --health`、`--print-connection`、`--check` 和 `thronectl`，并为这些 `Throne.exe` 命令加上 `--data-dir C:\ProgramData\Throne`：管理员并不是 LocalSystem，其默认数据目录是另一个目录。只有 Administrators 和 SYSTEM 可以打开该服务的管道并读取其密钥文件。

`Throne.exe` 是图形界面程序，因此命令提示符不会等待它结束。要等待它结束并读取其退出码：

```bat
start "" /wait "C:\Program Files\Throne\Throne.exe" --health --data-dir C:\ProgramData\Throne
echo %ERRORLEVEL%
```

`--print-connection` 打印的是 POSIX 的 `export` 行。在 PowerShell 中，请自行设置这些值：

```powershell
$env:THRONE_ADDR = '127.0.0.1:9095'
$env:THRONE_KEY_FILE = 'C:\ProgramData\Throne\config\api.key'
thronectl status
```

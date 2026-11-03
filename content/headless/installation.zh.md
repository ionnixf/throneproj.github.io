+++
title = "安装与服务"
description = "将无界面 Throne 安装为 systemd 服务、systemd 用户单元、Docker 容器、macOS LaunchDaemon 或 Windows 服务。"
weight = 10
toc = true
+++

无界面 Throne 就是以 `--headless` 启动的普通 Throne 程序。请按照[安装](@/get_started/installation.zh.md)中的说明安装 Throne，然后让服务管理器运行它。本页介绍在各个系统上的做法：

| 系统 | 服务管理器 | 运行身份 | 数据目录 |
| --- | --- | --- | --- |
| Debian、Ubuntu、Raspberry Pi OS、Fedora、openSUSE | [systemd 系统单元](#linux-packages) | root（或用户 `throne`） | `/var/lib/throne` |
| 任意 Linux，无需 root | [systemd 用户单元](#user-unit) | 你自己 | `~/.config/Throne` |
| Docker | [Docker 或 Docker Compose](#docker) | 容器中的 root | 卷 `/var/lib/throne` |
| macOS | [launchd LaunchDaemon](#macos) | root | `/Library/Application Support/Throne` |
| Windows | [Windows 服务](#windows) | LocalSystem | `C:\ProgramData\Throne` |

安装完成后，请按照[使用 thronectl 管理](@/headless/thronectl.zh.md)中的说明连接 `thronectl`。

## Debian、Ubuntu、Raspberry Pi OS、Fedora、openSUSE {#linux-packages}

来自[发布页面](https://github.com/throneproj/Throne/releases)和软件源的 `.deb` 与 `.rpm` 软件包会把 Throne 安装到 `/opt/Throne`，并安装 systemd 单元 `throne.service` 和环境文件 `/etc/throne/throne.env`。请根据系统的 C 库（glibc）选择软件包：

| 软件包 | 需要 | 可运行于，例如 |
| --- | --- | --- |
| `Throne-<version>-debian-amd64.deb`、`…-fedora-amd64.rpm` | glibc 2.28 | Debian 10、Ubuntu 20.04、RHEL 8 及更高版本、Fedora、openSUSE |
| `…-amd64-system-qt.deb`、`…-amd64-system-qt.rpm` | glibc 2.34 和系统的 Qt 6 | Debian 12、Ubuntu 22.04 及更高版本、Fedora、openSUSE Tumbleweed，使用发行版提供的 Qt |
| `Throne-<version>-debian-arm64.deb`、`…-fedora-arm64.rpm` | glibc 2.28 | arm64 上的 Raspberry Pi OS bullseye、Debian 10、Ubuntu 20.04 及更高版本 |
| `Throne-<version>-debian-arm64-system-qt.deb` | glibc 2.34 和系统的 Qt 6 | arm64 上的 Raspberry Pi OS bookworm、Debian 12、Ubuntu 22.04 及更高版本 |

每个软件包都依赖其文件所需的 glibc 版本，因此包管理器会拒绝安装对系统而言过新的软件包，而不是装上一个无法启动的 Throne。在树莓派上，请使用 64 位的 Raspberry Pi OS。在 bookworm 及更高版本上，两种 arm64 软件包都能使用。bullseye 没有 Qt 6，因此请在其上使用普通的 arm64 软件包。

不带 `system-qt` 的软件包自带 Qt，但仍然依赖系统的图形、字体和 X11 库，因为同一个 Throne 程序也包含桌面版。包管理器会安装这些库。不需要任何显示器。

安装软件包并启动服务：

```bash
sudo apt install ./Throne-<version>-debian-amd64.deb   # 或：sudo dnf install ./Throne-<version>-fedora-amd64.rpm
sudo /opt/Throne/Throne --check                        # 可选：这台电脑能否运行它？
sudo systemctl enable --now throne
```

- 软件包从不自行启用或启动该单元。升级时，如果该单元正在运行，会重启它；卸载软件包时会停止并禁用它。
- 卸载软件包后，`/var/lib/throne`（配置档、设置和 API 密钥）仍会保留，即使使用 `apt purge` 也是如此。要从头开始，请自行删除它。
- `systemctl start throne` 会在 Throne 启动完成后返回。参见 [systemd：就绪、状态与看门狗](@/headless/monitoring.zh.md#systemd)。
- `/etc/throne/throne.env` 为该单元可以使用的每个变量都提供了一个注释掉的示例。取消某一行的注释，然后运行 `sudo systemctl restart throne`。升级会保留你的修改。切勿把 API 密钥放进这个文件；请改用 `THRONE_API_KEY_FILE` 指定一个密钥文件。
- 日志：`journalctl -u throne -f`。
- 软件包还会把 `thronectl` 安装为 `/usr/bin/thronectl`，并安装用于 [Web 界面](@/headless/web.zh.md#systemd)的第二个单元 `throne-web.service`。它同样不会被启用。

要更改命令行，请用 `sudo systemctl edit throne` 添加一个 drop-in 文件：

```ini
[Service]
ExecStart=
ExecStart=/opt/Throne/Throne --headless --data-dir=%S/throne --debug
```

该单元以 root 身份运行 Throne。[Linux 上的权限](#linux-privileges)将这种方式与其他运行方式做了比较。

## 其他 Linux 安装方式 {#other-linux}

[安装脚本](@/get_started/installation.zh.md#linux-script)会把 Throne 放在 `/opt/Throne` 中，[便携版 ZIP](@/get_started/installation.zh.md#linux-zip) 则位于你解压到的位置。两者都不安装单元。请自行安装该单元：

```bash
curl -fsSLo throne.service https://raw.githubusercontent.com/throneproj/Throne/dev/dist/linux/throne.service
sudo install -m 644 throne.service /etc/systemd/system/throne.service
sudo systemctl daemon-reload
sudo systemctl enable --now throne
```

该单元运行 `/opt/Throne/Throne`。如果 Throne 在其他文件夹中，请像下面 AUR 软件包那样用 drop-in 文件修改这个路径。对这个单元来说，`/etc/throne/throne.env` 是可选的。如果想要其中的示例，请把 [`dist/linux/throne.env`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne.env) 保存到那里。

[AUR 软件包](@/get_started/installation.zh.md#aur)是单独维护的，它会把 Throne 安装到 `/usr/lib/throne`。如果它没有附带单元，请安装上面的单元，并用 drop-in 文件（`sudo systemctl edit throne`）让它指向该路径：

```ini
[Service]
ExecStart=
ExecStart=/usr/lib/throne/Throne --headless --data-dir=%S/throne
```

## Linux 上的权限 {#linux-privileges}

TUN 和 [Kill Switch](@/advanced/kill_switch.zh.md) 需要权限。使用[额外核心](@/advanced/chains.zh.md#extra-core)（Extra Core）的配置档需要一个普通用户来运行该核心，因为 Throne 从不以 root 身份运行额外核心。请选择该单元的运行方式：

| 单元的运行方式 | TUN | Kill Switch | 额外核心配置档 | 适用于 |
| --- | --- | --- | --- | --- |
| 以 root 身份（单元的原始配置） | 是 | 是 | 否 | 网关和专用设备 |
| 以 `throne` 身份并借助能力（`caps.conf`） | 是 | 否（`unsupported`） | 是 | 不需要 Kill Switch 的代理设备 |
| 以 `throne` 身份，搭配 setuid root 的 `ThroneCore` | 是 | 是 | 是 | 全部功能，且 Throne 本身无需以 root 身份运行 |
| 以 `throne` 身份，不做其他设置 | 否 | 否 | 是 | 仅代理（混合入站） |

**能力（capabilities）。** 下载 [`caps.conf`](https://github.com/throneproj/Throne/blob/dev/dist/linux/caps.conf)，然后运行：

```bash
sudo useradd --system --no-create-home --home-dir /var/lib/throne --shell /usr/sbin/nologin throne
sudo install -Dm644 caps.conf /etc/systemd/system/throne.service.d/caps.conf
sudo systemctl daemon-reload && sudo systemctl restart throne
```

下次启动时，systemd 会把 `/var/lib/throne` 交给 `throne` 所有。在这种配置下，`ThroneCore` 不能带有 setuid 位，也不能带有文件能力，因为运行这样的文件会丢弃该单元授予的能力。

**setuid 核心。** 使用一个在 `[Service]` 下只包含 `User=throne` 和 `Group=throne` 的 drop-in 文件，并让核心以 root 身份运行：

```bash
sudo chown root:root /opt/Throne/ThroneCore && sudo chmod u+s /opt/Throne/ThroneCore
```

软件包升级会用不带 setuid 位的文件替换 `ThroneCore`。在 Debian 和 Ubuntu 上，以下命令可以让该位在升级后保留：

```bash
sudo dpkg-statoverride --update --add root root 4755 /opt/Throne/ThroneCore
```

不要在该单元中添加 `NoNewPrivileges=`，也不要添加隐含它的加固选项：它会使 setuid 核心无法工作。

`thronectl status` 会在 `capabilities.tun` 和 `capabilities.kill_switch` 中显示这个 Throne 能做什么。核心会自行检查能否打开 `/dev/net/tun` 以及是否拥有 `CAP_NET_ADMIN`，因此没有 `NET_ADMIN` 的 root 容器会报告 `false`，而不是在之后才失败。

## systemd 用户单元 {#user-unit}

没有 root 权限时，Throne 以用户单元的形式在你自己的会话中运行。下载 [`throne-user.service`](https://github.com/throneproj/Throne/blob/dev/dist/linux/throne-user.service)，然后运行：

```bash
install -Dm644 throne-user.service ~/.config/systemd/user/throne.service
systemctl --user daemon-reload && systemctl --user enable --now throne
loginctl enable-linger "$USER"   # 开机时启动，无需登录
```

- 该单元使用桌面版的数据目录 `~/.config/Throne`，因此拥有相同的配置档和设置。请先退出桌面版。该单元运行期间，桌面版会提示 Throne 正在后台运行，然后退出。打开桌面版之前，请先运行 `systemctl --user stop throne`。
- TUN 和 Kill Switch 需要 setuid root 的核心。桌面版第一次请求权限时会完成这项设置（参见 [Linux 权限](@/guides/tun_mode.zh.md#privileges-linux)），`Throne --check` 也会打印所需的命令。
- 当你以自己的用户身份运行时，`Throne --health`、`Throne --print-connection` 和 `Throne --check` 无需任何选项就能找到这个 Throne。

## Docker {#docker}

镜像 `ghcr.io/throneproj/throne:<version>`（也有 `latest`）为 `linux/amd64` 和 `linux/arm64` 构建。它在 `/opt/Throne` 中包含 Throne，在 `/usr/local/bin` 中包含 `thronectl`，并以 `tini` 作为 init。它把数据保存在卷 `/var/lib/throne` 中，需要重启时以退出码 75 退出，并使用 `Throne --health` 作为健康检查。

请从 Throne 代码仓库中的 [`compose.yaml`](https://github.com/throneproj/Throne/blob/dev/docker/compose.yaml) 开始。它使用 `latest` 标签；把那里换成某个发布版标签，就可以按你自己的节奏更新。

```bash
curl -fsSLO https://raw.githubusercontent.com/throneproj/Throne/dev/docker/compose.yaml
docker compose up -d
docker compose exec throne thronectl --key-file /var/lib/throne/config/api.key status
```

该文件的重要部分：

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

注意事项：

- **代理端口。** 混合入站默认监听 `127.0.0.1`，发布的端口无法访问它。请运行 `thronectl settings set inbound_address=0.0.0.0`，然后重新启动配置档。Docker 发布的端口会绕过 `ufw` 等主机防火墙：在可以从互联网访问的主机上，请只发布局域网地址，例如 `"192.168.1.2:2080:2080"`。
- **TUN 和 Kill Switch** 需要 `cap_add: [NET_ADMIN]` 和设备 `/dev/net/tun`。没有它们，容器仍然可以提供混合入站。
- **主机网络。** 使用 `network_mode: host`（且没有 `ports:`）时，TUN 和 Kill Switch 作用于整个主机，并且主机上只能有一个 Throne 运行 TUN。参见[与桌面版 Throne 同时运行](@/headless/_index.zh.md#beside-desktop)。
- **停止。** 请保留 `stop_grace_period: 45s`。Docker 默认的 10 秒可能会在 Throne 停止其核心之前就将其杀死。使用 `restart: unless-stopped` 时，Docker 会在每次退出后重新启动 Throne，即使是在 `thronectl shutdown` 之后；请用 `docker compose stop` 停止它。
- **健康状态。** Docker 只会把容器标记为不健康，而不会重启它。
- **限制。** 额外核心配置档无法运行，因为容器以 root 身份运行。在 rootless Docker 和其他使用用户命名空间的容器中，Kill Switch 会以 `unsupported` 失败（参见 [Throne 如何保持可访问](@/headless/recovery.zh.md#protection)）。
- **选项。** 把变量写在 `environment:` 下，或者用 `command:` 添加参数，Docker 会把它们追加在 `--headless` 之后。只能用 `THRONE_DATA_DIR` 更改数据目录，因为健康检查要通过它找到 Throne。
- **不带 `--key-file` 使用 thronectl。** 在 `environment:` 下添加 `THRONE_KEY_FILE: /var/lib/throne/config/api.key`，这样 `docker compose exec throne thronectl status` 就可以直接使用。Throne 本身会忽略这个变量。
- **导入文件。** 使用 `-T` 通过管道传入：`docker compose exec -T throne thronectl import - < links.txt`。
- **从容器外部访问 API。** API 监听的是容器的环回地址。要从网络访问它，请设置 `THRONE_API_LISTEN: "*:9095"` 并提供 `THRONE_API_ALLOW` 列表，然后发布端口 9095。此时，导入和设置等管理请求还需要 `THRONE_API_ADMIN_LAN: "1"`，因为经过发布端口的每个请求都来自容器外部。在 Docker 的 NAT 之后，所有客户端共用同一个地址，因此一个使用旧密钥的客户端会把其他客户端锁在外面一分钟。
- **日志：** `docker compose logs -f throne`。
- **Web 界面。** 设置 `THRONE_WEB_LISTEN: "*:9096"` 并发布 9096 端口，容器就会同时提供 [Web 界面](@/headless/web.zh.md#docker)。

## macOS {#macos}

LaunchDaemon 会在开机时以 root 身份运行 Throne。TUN 和 Kill Switch 可以工作；额外核心配置档不能。请先把 `Throne.app` 移到 `/Applications`，下载 [`moe.Throne.headless.plist`](https://github.com/throneproj/Throne/blob/dev/dist/macos/moe.Throne.headless.plist)，然后运行：

```bash
sudo mkdir -p /Library/Logs/Throne
sudo install -m 644 -o root -g wheel moe.Throne.headless.plist /Library/LaunchDaemons/
sudo xattr -dr com.apple.quarantine /Applications/Throne.app   # 仅限未签名的版本
sudo launchctl bootstrap system /Library/LaunchDaemons/moe.Throne.headless.plist
```

- 停止并卸载它：`sudo launchctl bootout system/moe.Throne.headless`。
- 它的数据位于 `/Library/Application Support/Throne`，输出位于 `/Library/Logs/Throne/throne.log`。
- 该 plist 直接运行程序文件，因此 launchd 能跟踪该进程。切勿用 `open` 启动无界面 Throne：那样会把它交给 LaunchServices，并立即返回。
- launchd 会在 Throne 以错误退出 10 秒后再次启动它。它无法排除特定的退出码，因此对于重新启动也无法解决的退出，它同样会每 10 秒重试一次：另一个 Throne 正在使用该数据目录（退出码 3）、错误的选项（退出码 64）以及配置错误（退出码 78）。如果 Throne 不断启动又退出，请阅读日志。
- 查看它的状态：`sudo /Applications/Throne.app/Contents/MacOS/Throne --health`。

## Windows 服务 {#windows}

`Throne.exe --headless --service` 以原生 Windows 服务的形式在 LocalSystem 账户下运行，因此 TUN 和 Kill Switch 可以工作。

1. 使用安装程序为所有用户安装 Throne，安装到 `Program Files` 中。服务不能运行普通用户可以替换的程序，因此不要使用按用户安装的方式，也不要使用放在你用户文件夹中的 ZIP。
2. 下载 [`install-service.bat`](https://github.com/throneproj/Throne/blob/dev/dist/windows/install-service.bat) 和 [`uninstall-service.bat`](https://github.com/throneproj/Throne/blob/dev/dist/windows/uninstall-service.bat)。
3. 在以管理员身份运行的命令提示符中运行：

   ```bat
   install-service.bat
   ```

   如果 Throne 位于其他文件夹，请指定路径：`install-service.bat "D:\Apps\Throne\Throne.exe"`。

该脚本会创建服务 `Throne`，让它在开机后启动（延迟启动），并设置其恢复操作：

```bat
sc.exe failure Throne reset= 86400 actions= restart/3000/restart/10000/restart/60000
sc.exe failureflag Throne 1
```

- `failureflag 1` 让 Windows 在 Throne 以错误码退出时也执行恢复操作，而不仅仅是在崩溃之后。重启请求（退出码 75）和看门狗退出正是这样让 Throne 恢复运行的。每一次都算作一次失败：在一天之内，第一次重启在 3 秒后进行，第二次在 10 秒后，之后每次在 60 秒后。
- `sc.exe stop Throne` 和 `thronectl shutdown` 会让它彻底停止（退出码 0）。Windows 关机时，最多给 Throne 45 秒时间。
- 因错误停止后，`sc.exe query Throne` 会显示 `WIN32_EXIT_CODE : 1066`，并把 Throne 自己的退出码显示为 `SERVICE_EXIT_CODE`。
- 它的数据位于 `C:\ProgramData\Throne`，日志位于 `C:\ProgramData\Throne\config\logs\throne.log`。
- `uninstall-service.bat` 会停止并删除该服务。`C:\ProgramData\Throne` 中的数据会保留。
- 更新 Throne 之前，请先停止该服务。

任务计划程序中的计划任务无法替代该服务：Windows 会强行终止任务，因此 Throne 无法正常停止其核心；而且 Windows 也无法根据任务的退出码重新启动它。

### 数据文件夹 {#windows-data-folder}

该服务让 `C:\ProgramData\Throne` 只供 SYSTEM 和 Administrators 使用。它以这种权限创建该文件夹，并在每次启动时重新设置这些访问权限，对其中的文件也是如此。如果该文件夹已经存在，但属于其他用户、允许其他用户写入（你在 `C:\ProgramData` 下手动创建的文件夹就是这样），或者本身是链接或联接点（junction），或者其中有这样的文件或文件夹，服务就会以退出码 78 退出，并在日志中指出问题。请删除该文件夹，然后重新启动服务。普通用户可以写入的 `--data-dir` 只会在日志中产生一条警告。

### 在命令行窗口中运行命令 {#windows-prompt}

请在以管理员身份运行的命令行窗口中运行 `Throne.exe --health`、`--print-connection`、`--check` 和 `thronectl`，并为这些 `Throne.exe` 命令加上 `--data-dir C:\ProgramData\Throne`。管理员并不是 LocalSystem，因此其默认数据目录是另一个目录。只有 Administrators 和 SYSTEM 可以打开该服务的管道并读取其密钥文件。在未以管理员身份运行的命令行窗口中，`--health` 会记录 `[Instance] Not talking to … access denied` 并以退出码 2 退出。

`Throne.exe` 是 Windows 图形界面程序，因此命令提示符不会等待它结束。Throne 会把输出写到启动它的程序的控制台中。要等待它结束并读取其退出码：

```bat
start "" /wait "C:\Program Files\Throne\Throne.exe" --health --data-dir C:\ProgramData\Throne
echo %ERRORLEVEL%
```

```powershell
$p = Start-Process 'C:\Program Files\Throne\Throne.exe' -ArgumentList '--health', '--data-dir', 'C:\ProgramData\Throne' -Wait -PassThru
$p.ExitCode
```

`--print-connection` 打印的是 POSIX 的 `export` 行。在 PowerShell 中，请自行设置这些值：

```powershell
$env:THRONE_ADDR = '127.0.0.1:9095'
$env:THRONE_KEY_FILE = 'C:\ProgramData\Throne\config\api.key'
thronectl status
```

### 在控制台中运行 {#windows-console}

要在不使用服务的情况下测试无界面 Throne：

```bat
start "" /wait "C:\Program Files\Throne\Throne.exe" --headless
```

`Ctrl+C` 会让它正常停止，再按一次 `Ctrl+C` 会立即杀死核心。关闭控制台窗口、不带 `/F` 的 `taskkill /PID <pid>`、注销和关机也会让它正常停止。在服务之外，数据目录为 `%LOCALAPPDATA%\Throne`（以 LocalSystem 身份运行时为 `C:\ProgramData\Throne`）。重启会启动一个新的 Throne 进程并以退出码 0 退出，因此 `start /wait` 会返回，而新进程继续运行。

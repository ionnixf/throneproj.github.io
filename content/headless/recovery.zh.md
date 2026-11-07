+++
title = "防锁定与恢复"
description = "无界面 Throne 如何让 API 始终可以访问，以及当 Kill Switch 或 TUN 等已保存的设置把你锁在外面时，如何重新获得访问。"
weight = 50
toc = true
+++

## Throne 如何保持可访问 {#protection}

- **API 始终开启**，从首次启动起就在 `127.0.0.1:9095` 上。`--api-*` [选项](@/headless/command_line.zh.md#options)会覆盖已保存的 API 设置；从备份恢复设置时，会保留本设备的 API、入站、TUN 和 Kill Switch 设置。
- **密钥文件。** 空的 `api.key` 会获得一个新密钥。其他任何无法使用的密钥文件都会让 API 保持关闭：删除它，下次启动时就会创建新密钥。
- **API 的端口。** 当另一个程序占用了该端口时，Throne 会在没有 API 的情况下运行（`api_unavailable`），并每 30 秒重试一次。当另一个 Throne 占用了该端口时，请用 `--api-listen PORT` 为这个 Throne 指定另一个端口。
- **登录失败**从不会锁定环回地址，也不会锁定在过去 10 分钟内有客户端成功登录过的地址。
- **Kill Switch** 始终放行环回流量，以及其他地址上来自 API 端口的回复。在缺少 `CAP_SETGID` 的 rootless Docker 或其他使用用户命名空间的容器中，它会进入失败即关闭（fail closed）状态（`unsupported`），配置档会被拒绝，直到你关闭 `kill_switch` 设置。
- **Linux 上的 TUN** 可能会截走发给远程 API 客户端的回复，除非开启了 `tun_auto_redirect`；开启严格路由但未开启 `tun_ipv6` 时，IPv6 客户端会被丢弃。如果 API 监听网络地址时启动 TUN，Throne 会警告一次。
- **导致 Throne 崩溃的配置档：** 如果连续三次启动中，Throne 都在恢复它后一分钟内崩溃，Throne 就不会再恢复它（`resume_suppressed`）。请用 `thronectl connect` 启动它。
- **反复失败的核心：** 连续三次启动失败后，Throne 不再以退出码 70 或 71 退出，而是带着 API 保持运行（`core_gave_up`），并每 5 分钟重试一次核心。

## 恢复访问 {#recovery}

如果已保存的设置仍然把你锁在外面，例如 Kill Switch 中断了你的 SSH 会话，或者 TUN 截走了 API 的回复，请用 `--no-resume --no-kill-switch`（或变量 `THRONE_RESUME=0` 和 `THRONE_KILL_SWITCH=0`）启动 Throne 一次。这样不会启动任何配置档，Kill Switch 保持关闭，同一设备上的 `thronectl` 就可以修正设置，例如使用 `thronectl settings set kill_switch=false` 或 `thronectl tun off`。然后再正常启动 Throne。

**systemd：**

```bash
printf 'THRONE_RESUME=0\nTHRONE_KILL_SWITCH=0\n' | sudo tee -a /etc/throne/throne.env >/dev/null
sudo systemctl restart throne
thronectl settings set kill_switch=false        # 或：thronectl tun off
# 再从 /etc/throne/throne.env 中删除这两行，然后：
sudo systemctl restart throne
```

**Docker：** 在 `compose.yaml` 的 `environment:` 下添加 `THRONE_RESUME: "0"` 和 `THRONE_KILL_SWITCH: "0"`，然后运行 `docker compose up -d`。用 `docker compose exec throne thronectl …` 修正设置，删除这两个变量，再运行一次 `docker compose up -d`。

**macOS：** 把这两个变量添加到 plist 的 `EnvironmentVariables` 中，然后重新加载它。修正设置后，删除这两个变量并再次重新加载：

```bash
sudo launchctl bootout system/moe.Throne.headless
sudo launchctl bootstrap system /Library/LaunchDaemons/moe.Throne.headless.plist
```

**Windows 服务：** 用 `sc.exe stop Throne` 停止服务，然后在以管理员身份运行的命令行窗口中手动启动 Throne：

```bat
start "" /wait "C:\Program Files\Throne\Throne.exe" --headless --data-dir C:\ProgramData\Throne --no-resume --no-kill-switch
```

在另一个以管理员身份运行的命令行窗口中用 `thronectl` 修正设置（参见[在命令行窗口中运行命令](@/headless/installation.zh.md#windows-prompt)），用 `Ctrl+C` 停止 Throne，然后运行 `sc.exe start Throne`。

**从头开始：** 停止 Throne，并把它的数据目录移到别处（对于系统单元，即 `/var/lib/throne`）。下次启动时会创建一个新的空数据目录和新的 API 密钥。请从[备份](@/headless/thronectl.zh.md#from-desktop)中恢复你的配置档。

+++
title = "代理链与自定义配置"
description = "串联多个配置档、为分组添加前置与落地代理、编写原始 sing-box 或 Xray JSON，以及将其他代理程序作为额外核心运行。"
weight = 20
toc = true
+++

代理链、自定义配置、额外核心（Extra Core）以及分组的前置与落地代理仅在高级模式下可用（参见[简洁模式与高级模式](@/get_started/configuration.zh.md#interface-mode)）。

## 代理链 {#chains}

代理链配置档让流量从上到下依次经过多个配置档：如果 `A` 在最上面、`B` 在它下面，流量路径为：你的设备 → `A` → `B` → 互联网。每台服务器只能看到它前面的那台服务器，网站看到的是最下面的配置档。

### 创建代理链 {#create-chain}

1. 打开 `程序`（Program）→ `新建配置档`（New profile）（`Ctrl+N`），将 `类型`（Type）设为 `链式代理`（Chain Proxy），并填写 `名称`（Name）。
2. 点击 `选择配置档`（Select Profile），然后在主窗口中双击下一跳。为每个跳点重复此操作。
3. 拖动各行排成正确的顺序，然后点击 `确定`（OK）。

### 限制 {#chain-limits}

- 代理链中不能包含另一条代理链或自动选择器。自动选择器也会跳过代理链。
- `自定义 (sing-box 配置)`（Custom (sing-box config)）配置档不能作为跳点；`自定义 (sing-box 出站)`（Custom (sing-box outbound)）和 `自定义 (Xray 出站)`（Custom (Xray outbound)）配置档可以。
- 一条代理链可以包含一个额外核心或一个 `自定义 (Xray 配置)`（Custom (Xray config)）配置档，且它必须是最上面的跳点。`自定义 (Xray 配置)` 跳点不能与其他 Xray 跳点组合使用。
- 所有 Xray 跳点必须相邻，参见[代理链中的 Xray](@/advanced/xray.zh.md#xray-in-chains)。
- URL 测试会跳过包含额外核心或 `自定义 (Xray 配置)` 跳点的代理链。
- 如果代理链中没有额外核心或完整配置类的跳点，它可以作为路由规则的出站。
- 如果某个跳点已被删除，在你移除该跳点之前，代理链无法启动。

## 前置代理与落地代理 {#front-landing}

分组可以在其中每个配置档之前和之后各添加一个跳点：你的设备 → `前置代理`（Front Proxy）→ 你启动的配置档 → `落地代理`（Landing Proxy）→ WARP（如已开启）→ 互联网。当分组中的服务器只能通过另一台服务器访问时，前置代理会很有用；落地代理让整个分组使用同一个出口。

右键点击分组标签页 → `编辑选定分组`（Edit selected Group）即可设置。代理链的限制同样适用：两者都不能是代理链，设置了前置代理的分组无法启动额外核心或 `自定义 (Xray 配置)` 配置档。如果其中之一已被删除，在你更改它之前，分组中的配置档无法启动。另请参见[订阅与分组](@/guides/subscriptions.zh.md#front-landing-proxy)。

## 自定义配置 {#custom-config}

自定义配置档保存交给核心的原始 JSON，用于普通编辑器不提供的选项。`程序` → `新建配置档` 中有四种类型：

- `自定义 (sing-box 出站)`：一个 sing-box 出站或端点（endpoint）。
- `自定义 (sing-box 配置)`：一份完整的 sing-box 配置。
- `自定义 (Xray 出站)`：一个 Xray 出站。
- `自定义 (Xray 配置)`：一份完整的 Xray 配置。

`JSON 编辑器`（Json Editor）会按照 sing-box 的 schema 检查 sing-box JSON。如果想从 Throne 为某个可用的配置档或代理链生成的 JSON 开始编写，请右键点击它 → `分享`（Share）→ `导出配置`（Export config）。一个最简的 `自定义 (sing-box 出站)` 示例：

```json
{
  "type": "socks",
  "server": "203.0.113.10",
  "server_port": 1080
}
```

导入的 JSON 会成为普通配置档（参见[导入格式](@/reference/protocols.zh.md#import-formats)），但绝不会成为 `自定义 (sing-box 配置)`。这种类型只能手动创建。

### 完整配置 {#full-configs}

- **`自定义 (sing-box 配置)`** 会完全按原样运行。Throne 不会添加它自己的入站、TUN、路由配置档、DNS 设置、WARP 或前置与落地代理，因此你的配置必须自带入站。
- **`自定义 (Xray 配置)`** 会获得一个本地 SOCKS 入站，并在它前面接入 sing-box，因此 TUN 模式、路由配置档和 DNS 设置依然生效。在 Xray 内部，由你的配置中的 `routing` 选择出站。

完整配置不能作为路由规则的出站，也不能作为路由配置档的端点。自动选择器可以使用 `自定义 (Xray 配置)` 配置档，但会跳过 `自定义 (sing-box 配置)` 配置档。

## 额外核心 {#extra-core}

额外核心配置档会在你的电脑上运行另一个代理程序，用于 Throne 不支持的协议。该程序必须开启一个本地 SOCKS 服务器，供 Throne 连接。通过 `程序` → `新建配置档` → `类型` `额外核心`（Extra Core）来创建：

| 字段 | 作用 |
| --- | --- |
| `核心路径`（Core path） | 要运行的程序。 |
| `参数`（Args） | 命令行参数。`%s` 会被替换为一个临时文件的路径，该文件保存 `配置`（Config）中的文本。与在 shell 中一样，包含空格的值需要加引号。 |
| `配置`（Config） | 该程序配置文件的内容。只有当 `参数` 中包含 `%s` 时，它才会传递给程序。 |
| `Socks 地址`（Socks address）、`Socks 端口`（Socks port） | 该程序的 SOCKS 服务器（默认地址 `127.0.0.1`）。端口必须与程序的配置一致，并且不能与 Throne 自身的监听端口相同。 |
| `无日志`（No logs） | 不在 `日志`（Logs）标签页中显示该程序的输出。 |

例如，如果该程序通过 `-c <file>` 读取配置，就将 `参数` 设为 `-c %s`，并把配置粘贴到 `配置` 中。

该程序随配置档一起启动和停止；如果程序自行退出，Throne 会停止该配置档。即使 Throne 的核心为了 TUN 模式拥有管理员或 root 权限，该程序也以普通用户权限运行。额外核心配置档不能作为路由规则的出站，URL 测试和自动选择器也会跳过它。

## Android 版 {#android}

- 代理链：`添加服务器配置`（Add profile）→ `手动输入`（Manual settings）→ `链式代理`（Proxy chain），跳点顺序和限制相同。
- 分组同样有 `前置代理`（Front proxy）和 `落地代理`（Landing proxy）选项。
- `自定义配置`（Custom config）同样有四种类型，在 `配置类型`（Config type）中选择。
- 额外核心仅限桌面版。Android 会保留桌面版备份中的额外核心配置档，但无法启动它们。

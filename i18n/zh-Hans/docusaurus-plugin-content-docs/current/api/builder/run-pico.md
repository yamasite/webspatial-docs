---
sidebar_position: 2
description: '通过 ADB 在已连接的 PICO OS 6 设备上启动托管的 WebSpatial 应用。'
---

# `webspatial-builder run-pico`

在默认 ADB 设备上临时启动 Web App。该命令不会打包原生应用，也不会启动开发服务器。

## 前置条件

- 安装 `adb` 并加入 `PATH`。
- 连接希望由默认 ADB 目标选中的设备。
- 通过设备可访问的 HTTP 或 HTTPS URL 托管 Web 应用。

## 语法

```bash
webspatial-builder run-pico   [--base <url>]   [--manifest <path> | --manifest-url <url>]
```

| 选项             | 必填 | 行为                                                                                             |
| ---------------- | ---- | ------------------------------------------------------------------------------------------------ |
| `--base`         | 否   | 托管应用 URL。默认 `http://10.0.2.2:5173/`；缺少末尾 `/` 时自动补齐。不接受查询参数和 fragment。 |
| `--manifest`     | 否   | 相对于当前目录的 Manifest 路径，不能与 `--manifest-url` 同时使用。                               |
| `--manifest-url` | 否   | 下载远程 Manifest，不能与 `--manifest` 同时使用。                                                |

如果两个 Manifest 选项都未提供，Builder 依次检查 `public/manifest.json`、`public/manifest.webmanifest`，最后使用开发用 Manifest。它会用 `--base` 替换 `start_url` 和 `scope`，按该 base 解析图标 URL，再通过 PICO Web Router 以临时安装类型启动。

成功时输出 `Launched WebSpatial App on PICO OS 6 from <base>`。base 无效、Manifest 图标无效、同时提供两个 Manifest 选项、缺少 `adb` 或 ADB 启动失败时，命令会报错终止。

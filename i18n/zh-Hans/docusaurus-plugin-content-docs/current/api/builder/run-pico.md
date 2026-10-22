---
sidebar_position: 2
description: "无需打包原生应用，直接在 PICO OS 6 设备上启动 WebSpatial App 进行测试。"
---

# `webspatial-builder run-pico`

用于通过 adb 在 [PICO OS 6](https://developer.picoxr.com/document/web/web-app/) 设备上直接启动一个 [Web App](../../concepts/webspatial-app.md#web-app)。由于 PICO OS 6 已经内置 [WebSpatial Runtime](../../concepts/webspatial-app.md#webspatial-runtime)，无需原生打包——`run-pico` 会在 PICO Web App 运行时中按 URL 打开该 Web App。

## 快速参考 {#quick-reference}

| 项目           | 内容                                                        |
| -------------- | ----------------------------------------------------------- |
| 最适合         | 在真实的 PICO OS 6 设备上迭代 WebSpatial App。              |
| 是否需要打包？ | 不需要。PICO OS 6 直接按 URL 运行 Web App。                 |
| 默认 `--base`  | `http://10.0.2.2:5173/`（设备视角下的主机开发服务器地址）。 |

## 适用场景 {#when-to-use}

- 需要在真实的 [PICO OS 6](https://developer.picoxr.com/document/discover/pico-os-6-overview/) 设备上测试 WebSpatial App。
- 不想构建并安装原生应用包。

## 不适用场景 {#when-not-to-use}

- 目标平台没有内置 WebSpatial Runtime（例如 visionOS）时——请改用 [`run`](./run.md) 在模拟器中预览，或用 [`build`](./build.md) 在真机上预览。

## 语法 {#syntax}

```bash title="在 PICO OS 6 设备上启动" {2}
webspatial-builder run-pico \
  [--base=<base url>] \
  [--manifest=<local path>] \
  [--manifest-url=<url>]
```

## 选项总览

| 选项             | 是否必填 | 作用                                                             |
| ---------------- | -------- | ---------------------------------------------------------------- |
| `--base`         | 可选     | 设备上托管 Web App 的 base URL，默认为 `http://10.0.2.2:5173/`。 |
| `--manifest`     | 可选     | 指定本地 Web App Manifest 文件路径。                             |
| `--manifest-url` | 可选     | 直接从线上 URL 获取 Manifest。                                   |

## 选项 {#options}

### `--base`

设备访问 Web App 时使用的 base URL，默认为 `http://10.0.2.2:5173/`，即 PICO 设备视角下主机本地开发服务器的地址。如果开发服务器运行在其他主机或端口，请相应调整。

### `--manifest`

让 Builder 知道从当前项目的什么路径读取 Web App Manifest 文件。找不到 manifest 时，Builder 会使用一份最小默认 manifest（`name: "WebSpatialTest"`，`start_url: "/"`）。

### `--manifest-url`

让 Builder 直接通过 URL 获取线上网站中的 Web App Manifest。

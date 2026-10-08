---
sidebar_position: 3
description: '构建可安装到测试设备或用于发布流程的 Packaged WebSpatial App。'
---

# `webspatial-builder build`

用于在测试设备上预览 [Packaged WebSpatial App](../../concepts/webspatial-app.md#packaged-webspatial-app)。
它只负责打包；你仍然需要使用平台官方工具（比如 Xcode）把应用包推送到测试设备、完成安装并启动。
visionOS 测试设备需要[事先在 Apple 开发者账号控制台和 Xcode 中注册](../../how-to/app-store-connect.md#device-testing)。

## Quick Reference

| 项目               | 内容                                           |
| ------------------ | ---------------------------------------------- |
| 最适合             | 在个人测试设备上预览 Packaged WebSpatial App。 |
| CLI 必填选项       | `--base` 和 `--teamId`。                       |
| 是否上传到应用商店 | 不会。                                         |

## 适用场景 {#when-to-use}

- 要在不包含 [WebSpatial Runtime](../../concepts/webspatial-app.md#webspatial-runtime) 的[空间计算](../../concepts/spatial-computing.md)平台（比如 visionOS）上运行 [WebSpatial App](../../concepts/webspatial-app.md)。
- 只需要[在个人测试设备上预览效果](../../how-to/app-store-connect.md#device-testing)。

## 不适用场景 {#when-not-to-use}

- 要在内置了 [WebSpatial Runtime](../../concepts/webspatial-app.md#webspatial-runtime)、支持直接用 URL 运行 [WebSpatial App](../../concepts/webspatial-app.md) 的[空间计算](../../concepts/spatial-computing.md)平台上运行，比如 [PICO OS 6](https://developer.picoxr.com/document/web/web-app/)。
- 只需要[在模拟器里预览](./run.md)，或要[对外大量分发应用](./publish.md)。

## 语法 {#syntax}

```bash title="为测试设备构建应用包" {2-3}
webspatial-builder build \
  --base=<base url or path> \
  --teamId=<id> \
  [--bundle-id=<id>] \
  [--platform=visionos] \
  [--version=<version>] \
  [--buildType=<type>] \
  [--export=<local path>] \
  [--manifest=<local path>] \
  [--manifest-url=<url>] \
  [--project=<dist>]
```

## 选项总览

| 选项             | 是否必填       | 作用                                                                                                               |
| ---------------- | -------------- | ------------------------------------------------------------------------------------------------------------------ |
| `--base`         | 必填           | 提供或覆盖用于解析 `start_url` 的 base URL 或路径。                                                                |
| `--teamId`       | 必填           | 指定用于签名构建结果的 Apple 开发团队。                                                                            |
| `--bundle-id`    | Commander 可选 | 指定已注册的 Apple 应用标识符。                                                                                    |
| `--platform`     | 可选           | 选择平台；当前只实现 `visionos`，也是默认值。                                                                      |
| `--version`      | 可选           | 设置应用版本；生成 Xcode 项目时默认 `1.0`。                                                                        |
| `--buildType`    | 可选           | 可选值为 `release-testing`、`app-store-connect`、`debugging`、`enterprise`；缺少或无效时回退到 `release-testing`。 |
| `--export`       | 可选           | 修改原生应用包的导出路径。                                                                                         |
| `--manifest`     | 可选           | 指定本地 Web App Manifest 文件路径。                                                                               |
| `--manifest-url` | 可选           | 直接从线上 URL 获取 Manifest。                                                                                     |
| `--project`      | 可选           | 指定自定义的网站构建产物目录。                                                                                     |

## 选项 {#options}

### `--teamId`

在 visionOS 设备上预览效果时必填，值来自 [Apple 开发者账号控制台开发者信息中的 Team ID](../../how-to/app-store-connect.md#prerequisites)。

### `--bundle-id`

Commander 不强制必填。用于覆盖 Manifest 中的 App ID，值可来自[在 Apple 开发者账号控制台注册 App 后得到的 Bundle ID](../../how-to/app-store-connect.md#prerequisites)。

### `--export`

WebSpatial Builder 生成的原生应用包默认会写入 `build/` 目录，可以用 `--export` 改成自定义路径。

### `--base`

为当前 [Web App](../../concepts/webspatial-app.md#web-app) 的起始网址（[`start_url`](https://developer.mozilla.org/en-US/docs/Web/Manifest/start_url)）指定 base URL 或路径。该选项由 Commander 强制要求。

如果 WebSpatial Builder 不知道当前 Web App 的 `start_url`，`--base` 的值会被直接作为 `start_url`。

如果 WebSpatial Builder 获取到的 `start_url` 是[相对地址，缺少域名部分](#--manifest)，可以通过 `--base` 提供域名，补全 URL。

| 解析状态               | 运行时行为                                                                                                                                             |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 已有完整的 `start_url` | Packaged App 在运行过程中会从线上加载 HTML/CSS/JS 等 Web 文件。                                                                                        |
| 缺少完整的 `start_url` | WebSpatial Builder 会把[网站构建产物](../../concepts/webspatial-app.md#web-build-tool)打包进原生应用包里，运行时从应用包内的本地文件离线加载网站内容。 |

如果 WebSpatial Builder 已经拿到当前 Web App 的完整 `start_url`，可以用 `--base` 修改 `start_url` 的 base 部分。

### `--manifest`

让 WebSpatial Builder 知道从当前项目的什么路径读取 Web App Manifest 文件。默认会从 `public/manifest.webmanifest` 或 `public/manifest.json` 读取。

Web App Manifest 里的 `start_url` 等字段通常都是相对地址，不会写死域名等 base URL 信息，所以 WebSpatial Builder 仅凭仓库里的 manifest 文件无法确定如何通过 URL 访问当前 Web App，需要依靠 [`--base`](#--base) 获取域名并补全 URL。否则生成的 Packaged App 会包含所有[网站构建产物](../../concepts/webspatial-app.md#web-build-tool)，而不是从线上加载网站。

### `--manifest-url`

让 WebSpatial Builder 直接通过 URL 获取线上网站中的 Web App Manifest。

由于这个 URL 自带域名等 base 信息，即使 Manifest 文件中的 `start_url` 是相对地址，WebSpatial Builder 也可以像浏览器一样自动推导并补全 `start_url`。在这种情况下，Builder 相当于直接把线上的 Web App 以 URL 形式嵌入 Packaged App，不再需要本地项目文件，也不再依赖 [`--base`](#--base) 等参数。

### `--project`

如果 WebSpatial Builder 无法确定如何通过 URL 访问当前 Web App，默认会从 `dist/` 读取[网站构建产物](../../concepts/webspatial-app.md#web-build-tool)并打包进原生应用包里。

可以用 `--project` 让 WebSpatial Builder 从其他路径读取网站构建产物。

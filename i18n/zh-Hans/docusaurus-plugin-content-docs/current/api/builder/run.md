---
sidebar_position: 1
description: '在模拟器中启动 Packaged WebSpatial App，用于本地预览和调试。'
---

# `webspatial-builder run`

用于在模拟器中预览 [Packaged WebSpatial App](../../concepts/webspatial-app.md#packaged-webspatial-app)。
它会自动完成打包、启动模拟器、把应用包传进模拟器、安装并启动应用。

## Quick Reference

| 项目             | 内容                                                |
| ---------------- | --------------------------------------------------- |
| 最适合           | 在模拟器里预览 Packaged WebSpatial App。            |
| 是否需要真机凭证 | 不需要。                                            |
| 常见目标平台     | 没有内置 WebSpatial Runtime 的平台，比如 visionOS。 |

## 适用场景 {#when-to-use}

- 要在不包含 [WebSpatial Runtime](../../concepts/webspatial-app.md#webspatial-runtime) 的[空间计算](../../concepts/spatial-computing.md)平台（比如 visionOS）上运行 [WebSpatial App](../../concepts/webspatial-app.md)。
- 只需要[在模拟器里预览效果](../../introduction/getting-started.md#preview)，不需要真机测试。

## 不适用场景 {#when-not-to-use}

- 要在内置了 [WebSpatial Runtime](../../concepts/webspatial-app.md#webspatial-runtime)、支持直接用 URL 运行 [WebSpatial App](../../concepts/webspatial-app.md) 的[空间计算](../../concepts/spatial-computing.md)平台上运行，比如 [PICO OS 6](https://developer.picoxr.com/document/web/web-app/)。
- 需要[在真机上测试](./build.md)或要[对外大量分发应用](./publish.md)。

## 语法 {#syntax}

```bash title="在模拟器中预览 Packaged WebSpatial App" {2}
webspatial-builder run \
  [--base=<base url>] \
  [--manifest=<local path>] \
  [--manifest-url=<url>] \
  [--project=<dist>] \
  [--platform=<platform>] \
  [--bundle-id=<id>] \
  [--tryWithoutBuild=<true|false>]
```

## 选项总览

| 选项                | 是否必填 | 作用                                                             |
| ------------------- | -------- | ---------------------------------------------------------------- |
| `--base`            | 可选     | 提供或覆盖用于解析 `start_url` 的 base URL。                     |
| `--manifest`        | 可选     | 指定本地 Web App Manifest 文件路径。                             |
| `--manifest-url`    | 可选     | 直接从线上 URL 获取 Manifest。                                   |
| `--project`         | 可选     | 指定自定义的网站构建产物目录。                                   |
| `--platform`        | 可选     | 选择打包平台；当前只实现 `visionos`，也是默认值。                |
| `--bundle-id`       | 可选     | 覆盖 Manifest App ID；开发模式默认回退到 `com.webspatial.test`。 |
| `--tryWithoutBuild` | 可选     | 只有字符串 `true` 会尝试复用相同的上一次命令和已安装测试 App。   |

## 选项 {#options}

### `--base`

为当前 [Web App](../../concepts/webspatial-app.md#web-app) 的起始网址（[`start_url`](https://developer.mozilla.org/en-US/docs/Web/Manifest/start_url)）指定 base URL。默认值是 `""`。

如果 WebSpatial Builder 不知道当前 Web App 的 `start_url`，`--base` 的值会被直接作为 `start_url`。

如果 WebSpatial Builder 获取到的 `start_url` 是[相对地址，缺少域名部分](#--manifest)，可以通过 `--base` 提供域名，补全 URL。

| 解析状态               | 运行时行为                                                                                                                                             |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 已有完整的 `start_url` | Packaged App 在运行过程中会从线上加载 HTML/CSS/JS 等 Web 文件。                                                                                        |
| 缺少完整的 `start_url` | WebSpatial Builder 会把[网站构建产物](../../concepts/webspatial-app.md#web-build-tool)打包进原生应用包里，运行时从应用包内的本地文件离线加载网站内容。 |

:::tip[开发阶段优先提供 `--base`]
在开发调试阶段，离线打包模式会导致每次代码修改后都要重新运行 Builder，并等待打包和安装完成。只要条件允许，都建议提供 `--base`。
:::

如果 WebSpatial Builder 已经拿到当前 Web App 的完整 `start_url`，可以用 `--base` 修改 `start_url` 的 base 部分。

### `--manifest`

让 WebSpatial Builder 知道从当前项目的什么路径读取 Web App Manifest 文件。默认会从 `public/manifest.webmanifest` 或 `public/manifest.json` 读取。

:::info[run 的临时 Manifest]
`run` 命令在找不到 manifest 时，会使用下面这份临时 manifest，以减少项目早期接入时的准备工作：

```json5 title="run 使用的临时 manifest"
{
  name: "WebSpatialTest",
  display: "minimal-ui",
  start_url: "/",
  scope: "/",
}
```

:::

Web App Manifest 里的 `start_url` 等字段通常都是相对地址，不会写死域名等 base URL 信息，所以 WebSpatial Builder 仅凭仓库里的 manifest 文件无法确定如何通过 URL 访问当前 Web App，需要依靠 [`--base`](#--base) 获取域名并补全 URL。否则生成的 Packaged App 会包含所有[网站构建产物](../../concepts/webspatial-app.md#web-build-tool)，而不是从线上加载网站。

### `--manifest-url`

让 WebSpatial Builder 直接通过 URL 获取线上网站中的 Web App Manifest。

由于这个 URL 自带域名等 base 信息，即使 Manifest 文件中的 `start_url` 是相对地址，WebSpatial Builder 也可以像浏览器一样自动推导并补全 `start_url`。在这种情况下，Builder 相当于直接把线上的 Web App 以 URL 形式嵌入 Packaged App，不再需要本地项目文件，也不再依赖 [`--base`](#--base) 等参数。

### `--project`

如果 WebSpatial Builder 无法确定如何通过 URL 访问当前 Web App，默认会从 `dist/` 读取[网站构建产物](../../concepts/webspatial-app.md#web-build-tool)并打包进原生应用包里。

可以用 `--project` 让 WebSpatial Builder 从其他路径读取网站构建产物。

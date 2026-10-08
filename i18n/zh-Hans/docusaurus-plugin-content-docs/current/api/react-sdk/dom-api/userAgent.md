---
sidebar_position: 1
description: '读取当前设备、操作系统，以及是否运行在 WebSpatial 模式等运行时信息。'
---

# `userAgent`

## 概述 {#summary}

获取当前设备、操作系统和运行环境的信息，包括 [Web Runtime](../../../concepts/webspatial-app.md#web-runtime) 和 [WebSpatial Runtime](../../../concepts/webspatial-app.md#webspatial-runtime) 的信息。

:::tip[请用能力查询控制功能分支]
当应用逻辑取决于某个组件、API、事件或选项是否可用时，请使用 [`WebSpatialRuntime.supports`](../js-api/WebSpatialRuntime.md)。User Agent 解析用于描述运行环境，但它不是公开的能力支持契约。
:::

## 可用对象 {#exposed-on}

`navigator` 对象上可以访问这个属性。

## 心智模型 {#mental-model}

如果从 User Agent 字符串中能匹配到 `/WebSpatial\/(\S+)/`，说明当前运行环境有 WebSpatial Runtime，版本号是这个 Runtime 能全面支持的 [React SDK](../../../introduction/getting-started.md#installation) 的 npm 包版本。

如果从 User Agent 字符串中能匹配到 `/\sVR\s/`，说明当前运行环境支持 WebXR。

如果从 User Agent 字符串中能匹配到 `\swv\)`，说明当前运行环境的 Web Runtime 是 WebView。

如果从 User Agent 字符串中能匹配到 `/WSAppShell\/(\S+)/`，说明当前运行环境是自带 WebSpatial Runtime 的 [Packaged WebSpatial App](../../../concepts/webspatial-app.md#packaged-webspatial-app)。如果还能匹配到 `"Macintosh"`，说明是 visionOS 环境。

如果从 User Agent 字符串中能匹配到 `/PicoWebApp\/(\S+)/`，说明当前运行环境是 [PICO OS 6 的 Web App Runtime](https://developer.picoxr.com/document/web/web-platform/)。

## 读写语义 {#read--write-semantics}

只读。

示例：

```js
const ua = navigator.userAgent.toString();
```

## 回退行为 {#fallback-behavior}

在不支持 WebSpatial（没有 WebSpatial Runtime）的环境里，User Agent 字符串里不存在 `WebSpatial` 字段。

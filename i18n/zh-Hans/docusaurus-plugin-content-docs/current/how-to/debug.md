---
sidebar_position: 7
description: '安装控制台辅助工具，在开发期间检视运行中的空间场景与空间化元素。'
---

# 调试空间场景

## 概述 {#summary}

`enableDebugTool()` 在 `window` 上安装两个辅助函数，让你可以从控制台检视运行中的空间场景，并触达空间化 2D 元素：

```js
import { enableDebugTool } from "@webspatial/react-sdk";
```

在服务器端（`window` 未定义）该调用为空操作。

## 前置条件 {#prerequisites}

这些工具依赖活动的空间会话才能真正工作，因此请在 [`bootSpatial()`](../api/react-sdk/js-api/bootSpatial.md) resolve 之后调用 `enableDebugTool()`——例如在 [`<SpatialBoot>`](../api/react-sdk/react-components/SpatialBoot.md) 的成功状态内部，或在命令式 `await bootSpatial()` 之后。在 boot 之前调用场景检视函数会抛出错误，提示会话尚不存在。

## 安装辅助工具 {#install-the-helpers}

```js
import { bootSpatial, enableDebugTool } from "@webspatial/react-sdk";

async function setupDebugTools() {
  await bootSpatial();
  enableDebugTool();
}
```

## `window.inspectCurrentSpatialScene()` {#windowinspectcurrentspatialscene}

异步函数，返回对当前空间场景做检视的结果：

```js
await window.inspectCurrentSpatialScene();
```

当尚不存在空间会话（boot 未完成）时，它会抛出 `inspectCurrentSpatialScene: no spatial session…`。

## `window.getSpatialized2DElement(el)` {#windowgetspatialized2delementel}

返回某个空间化 2D DOM 节点背后的内部空间化元素，便于直接检视：

```js
const el = document.querySelector("[enable-xr]");
const spatialized = window.getSpatialized2DElement(el);
```

## 配套浮层 {#companion-overlay}

如果需要可视化检视器，可以渲染来自 `@webspatial/react-sdk/experimental` 的实验性 [`<WebSpatialInspector>`](../api/react-sdk/experimental/index.md#webspatialinspector)——一个基于 ornament 的检视浮层。

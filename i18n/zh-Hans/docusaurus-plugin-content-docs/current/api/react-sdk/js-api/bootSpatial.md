---
sidebar_position: 4
description: '启动空间运行时、追踪就绪状态并处理加载失败；涵盖 lazy 与 eager 两种分发形态。'
---

# `bootSpatial` 与就绪 API

## 概述 {#summary}

默认入口按需懒加载空间实现。`bootSpatial()` 是触发加载的唯一激活入口——空间组件和 Hook 自身不会触发加载。配套的就绪 API 让你观察加载何时完成、在失败时做出反应。

```js
import {
  bootSpatial,
  isSpatialReady,
  useSpatialReady,
  onSpatialLoadError,
  WebSpatialBootError,
} from "@webspatial/react-sdk";
```

在 React 应用中，用 [`<SpatialBoot>`](../react-components/SpatialBoot.md) 包裹空间界面时，它已经替你调用 `bootSpatial()`。下面这些 API 用于手动控制、命令式入口和错误处理。

## `bootSpatial()` {#bootspatial}

### 函数签名 {#signature}

```ts
bootSpatial(): Promise<void>;
```

### 行为 {#behavior}

- **非运行时下为空操作。** 检测不到空间运行时（普通浏览器或服务器端）时，Promise 立即 resolve，不发起任何网络请求。
- **幂等。** 并发调用共享同一个进行中的 Promise；成功 resolve 之后，后续调用直接返回已完成的 Promise，不再安排额外工作。
- **可重试。** reject 之后再次调用 `bootSpatial()` 会重新发起一次动态 import。
- **失败。** 失败时 Promise 以 [`WebSpatialBootError`](#webspatialbooterror) reject。

### 示例：命令式入口 {#example-imperative-entry}

```js
import { bootSpatial } from "@webspatial/react-sdk";
import { createRoot } from "react-dom/client";
import { App } from "./App.jsx";

await bootSpatial();
createRoot(document.getElementById("root")).render(<App />);
```

## `isSpatialReady()` {#isspatialready}

### 函数签名 {#signature-1}

```ts
isSpatialReady(): boolean;
```

同步读取。只有在空间 chunk 成功加载、bridge 已持有实现之后才返回 `true`。在 boot 之前、失败时以及非 WebSpatial 环境中都返回 `false`。

## `useSpatialReady()` {#usespatialready}

### 函数签名 {#signature-2}

```ts
useSpatialReady(): boolean;
```

订阅 bridge 就绪状态的 React Hook：空间 chunk 就绪时消费组件会自动重渲染。

- SSR 期间返回 `false`。
- 在非 WebSpatial 浏览器中保持 `false`，且不产生每次渲染的订阅开销。

```jsx
import { useSpatialReady } from "@webspatial/react-sdk";

function StatusBadge() {
  const ready = useSpatialReady();
  return ready ? <span>空间就绪</span> : <span>加载中…</span>;
}
```

## `onSpatialLoadError()` {#onspatialloaderror}

### 函数签名 {#signature-3}

```ts
onSpatialLoadError(listener: (error: WebSpatialBootError) => void): () => void;
```

注册一个在每次加载失败时调用的回调，返回取消订阅函数。

- 监听器按注册顺序调用，每次失败只触发一次。
- 失败后成功重试时，**不会**把之前的错误重放给监听器。
- 监听器内部抛出的错误是隔离的：开发环境只做日志输出，绝不会污染 boot Promise 或替换 reject 原因。

```js
import { onSpatialLoadError, bootSpatial } from "@webspatial/react-sdk";

const unsubscribe = onSpatialLoadError((err) => {
  console.error(`第 ${err.attempt} 次启动失败：`, err.cause);
});

await bootSpatial().catch(() => {
  // 显示“重试”按钮；可以再次调用 bootSpatial()。
});
```

## `WebSpatialBootError` {#webspatialbooterror}

`bootSpatial()` 的 reject 值，也是 `onSpatialLoadError` 回调收到的参数。

```ts
class WebSpatialBootError extends Error {
  readonly name: "WebSpatialBootError";
  readonly cause: unknown; // 原始的动态 import 失败。
  readonly attempt: number; // 从 1 开始的重试序号。
}
```

## 分发形态：lazy 与 eager {#distribution-forms-lazy-vs-eager}

这个包提供两个 import 根：

|                      | `@webspatial/react-sdk`（默认） | `@webspatial/react-sdk/eager` |
| -------------------- | ------------------------------- | ----------------------------- |
| 面向场景             | 从网页渐进增强到空间的应用      | 纯空间应用（固定空间设备）    |
| 空间 chunk           | `bootSpatial()` 时动态 import   | 静态打进 bundle               |
| 网络请求数           | 两次（页面 + 空间 chunk）       | 一次                          |
| `bootSpatial()` 往返 | 必需                            | 无                            |

在 eager 入口上，上面四个 API 以兼容桩形式存在，因此迁移时**只改 import 根**——改路径，不改符号：

- `bootSpatial()` 立即 resolve（并在开发环境打印一次性警告，提示 boot 已无必要）。
- `isSpatialReady()` 和 `useSpatialReady()` 恒返回 `true`。
- `onSpatialLoadError()` 注册回调但永不触发——eager 入口没有动态加载，也就没有加载失败。

:::caution[不要混用 import 根]
每个应用 bundle（包括传递依赖）只能使用一个 import 根。如果同一个 JavaScript realm 中同时加载了 `@webspatial/react-sdk` 和 `@webspatial/react-sdk/eager`，后一个入口会在模块求值时抛出 `WebSpatialMixedEntryError`。错误暴露 `previous` 和 `next`（取值 `"lazy"` 或 `"eager"`），消息中会写明两条 import 路径。
:::

## 相关 API {#related-apis}

- [`<SpatialBoot>`](../react-components/SpatialBoot.md)——替你调用 `bootSpatial()` 的 React 包装组件。
- [`WebSpatialRuntime`](WebSpatialRuntime.md)——boot 完成后探测运行时能力。
- [`userAgent`](../dom-api/userAgent.md)——低层运行时检测。

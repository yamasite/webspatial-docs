---
sidebar_position: 1
description: '在 WebSpatial 运行时中按需加载空间实现。'
---

# `bootSpatial`

应用运行在 WebSpatial 运行时时，按需加载空间实现。普通 React 集成优先使用 [`<SpatialBoot>`](../../react-components/SpatialBoot.md)；需要命令式控制时再直接调用 `bootSpatial()`。

## 签名

```ts
import { bootSpatial } from "@webspatial/react-sdk";

function bootSpatial(): Promise<void>;
```

## 行为

| 环境                          | 结果                                                                             |
| ----------------------------- | -------------------------------------------------------------------------------- |
| WebSpatial 运行时             | 动态加载空间实现，只有就绪后 Promise 才会 resolve。                              |
| 普通浏览器                    | 直接 resolve，不加载空间 chunk，继续使用 Web fallback。                          |
| 服务端渲染                    | 不访问 `window`，也不发起动态加载，直接 resolve。                                |
| `@webspatial/react-sdk/eager` | 空间实现已经静态链接，因此立即 resolve。开发环境第一次调用会提示 boot 没有必要。 |

同一次加载过程中的并发调用会共用同一个底层加载任务。首次成功后，后续调用直接使用缓存结果，不会再次发起请求。

加载失败时，Promise 会 reject 一个 [`WebSpatialBootError`](./WebSpatialBootError.md)。之后再次调用会开始一次新的尝试，同一时间只会有一个重试在进行。

## 示例：在纯 CSR 渲染前启动

```tsx
import { bootSpatial } from "@webspatial/react-sdk";
import { createRoot } from "react-dom/client";
import { App } from "./App";

await bootSpatial();
createRoot(document.getElementById("root")!).render(<App />);
```

SSR 和 hydration 必须使用默认入口。可以在 `hydrateRoot()` 前或后启动；hydration 阶段仍使用 fallback 标记，真实空间实现会在之后的 React commit 中挂载。不要在服务端渲染从 `@webspatial/react-sdk/eager` 导入的空间组件。

:::caution[一个应用只用一个入口]
不要在同一个应用 bundle 中混用 `@webspatial/react-sdk` 和 `@webspatial/react-sdk/eager`。详见 [`WebSpatialMixedEntryError`](./WebSpatialMixedEntryError.md)。
:::

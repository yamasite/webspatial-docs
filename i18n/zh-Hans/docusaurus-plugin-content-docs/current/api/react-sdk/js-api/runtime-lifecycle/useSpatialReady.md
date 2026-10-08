---
sidebar_position: 3
description: '让 React 组件订阅空间运行时的就绪状态。'
---

# `useSpatialReady`

返回当前就绪状态；默认入口的运行时就绪后，会让组件重新渲染。

## 签名

```ts
import { useSpatialReady } from "@webspatial/react-sdk";

function useSpatialReady(): boolean;
```

## 返回值

- 在 WebSpatial 运行时中初始为 `false`，[`bootSpatial()`](./bootSpatial.md) resolve 后变为 `true`。
- 在普通浏览器和服务端渲染中返回 `false`，且不订阅运行时变化。
- 从 `@webspatial/react-sdk/eager` 导入时始终返回 `true`。

hook 会在组件实例挂载时判断一次运行时类型。测试工具如果在挂载后修改运行环境，需要重新挂载组件才能重新判断。

服务端快照固定为 `false`。因此，即使 boot 在 `hydrateRoot()` 前已经完成，首次客户端 hydration 仍会与服务端 fallback 标记一致；真实空间实现在 hydration commit 之后切换。

## 示例

```tsx
import { useSpatialReady } from "@webspatial/react-sdk";

export function RuntimeStatus() {
  const ready = useSpatialReady();
  return <span>{ready ? "Spatial ready" : "Web fallback"}</span>;
}
```

调用这个 hook 不会启动加载。请使用 [`<SpatialBoot>`](../../react-components/SpatialBoot.md) 或调用 [`bootSpatial()`](./bootSpatial.md)。

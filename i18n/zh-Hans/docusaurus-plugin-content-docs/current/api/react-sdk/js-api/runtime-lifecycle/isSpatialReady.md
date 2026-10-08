---
sidebar_position: 2
description: '同步读取当前空间运行时是否就绪。'
---

# `isSpatialReady`

返回当前 JavaScript realm 中的空间实现是否已经就绪。

## 签名

```ts
import { isSpatialReady } from "@webspatial/react-sdk";

function isSpatialReady(): boolean;
```

## 返回值

- 默认入口成功加载并保存空间实现后返回 `true`。
- 启动前、加载中、加载失败后、普通浏览器和 SSR 中返回 `false`。
- 从 `@webspatial/react-sdk/eager` 导入时始终返回 `true`。

这是同步快照，不会订阅变化，也不会启动加载。React 组件需要随状态变化重新渲染时，使用 [`useSpatialReady`](./useSpatialReady.md)。

```tsx
if (isSpatialReady()) {
  startSpatialOnlyWork();
}
```

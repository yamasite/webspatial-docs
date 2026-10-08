---
sidebar_position: 4
description: '订阅默认入口加载空间运行时失败的事件。'
---

# `onSpatialLoadError`

注册空间实现加载失败监听器。

## 签名

```ts
import { onSpatialLoadError } from "@webspatial/react-sdk";

function onSpatialLoadError(
  listener: (error: WebSpatialBootError) => void,
): () => void;
```

## 行为

- 每次加载失败，每个仍有效的监听器按注册顺序调用一次。
- 收到的 [`WebSpatialBootError`](./WebSpatialBootError.md) 与 [`bootSpatial()`](./bootSpatial.md) reject 的错误是同一个实例。
- 返回的函数用于取消订阅，之后的失败不再调用该监听器。
- 重试成功不会重复上报之前的错误。
- 普通浏览器和 SSR 不会尝试加载，因此监听器不会调用。
- 在 `@webspatial/react-sdk/eager` 中，注册只是兼容空操作，监听器永远不会触发。

某个监听器抛错不会替换原始 boot rejection，也不会阻止后续监听器运行。开发环境会把监听器异常输出到控制台。

## 示例

```ts
import { onSpatialLoadError } from "@webspatial/react-sdk";

const unsubscribe = onSpatialLoadError((error) => {
  reportRuntimeFailure({
    attempt: error.attempt,
    cause: error.cause,
  });
});

unsubscribe();
```

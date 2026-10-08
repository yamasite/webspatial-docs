---
sidebar_position: 5
description: '检查空间运行时加载失败及对应的重试次数。'
---

# `WebSpatialBootError`

默认入口无法加载空间实现时使用的错误类。

## 结构

```ts
import { WebSpatialBootError } from "@webspatial/react-sdk";

class WebSpatialBootError extends Error {
  readonly cause: unknown;
  readonly attempt: number;
}
```

| 属性      | 含义                                               |
| --------- | -------------------------------------------------- |
| `name`    | 固定为 `"WebSpatialBootError"`。                   |
| `message` | `Failed to boot WebSpatial runtime on attempt N`。 |
| `cause`   | 动态 import reject 的原始值。                      |
| `attempt` | 从 1 开始的加载尝试序号；失败后重试会递增。        |

`bootSpatial()` 不会直接暴露动态 import 的原始错误，而是 reject 这个包装错误。同一次尝试中，所有 [`onSpatialLoadError`](./onSpatialLoadError.md) 监听器收到的是同一个实例。

```ts
import {
  bootSpatial,
  WebSpatialBootError,
} from "@webspatial/react-sdk";

try {
  await bootSpatial();
} catch (error) {
  if (error instanceof WebSpatialBootError) {
    console.error(`Boot attempt ${error.attempt} failed`, error.cause);
  }
}
```

`@webspatial/react-sdk/eager` 也导出这个类，便于保持源码兼容；但 eager 模式不会动态加载运行时，因此 boot 不会抛出它。

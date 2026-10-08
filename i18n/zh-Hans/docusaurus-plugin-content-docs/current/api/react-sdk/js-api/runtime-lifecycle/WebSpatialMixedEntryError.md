---
sidebar_position: 6
description: '诊断同一 bundle 同时加载 lazy 与 eager React SDK 入口的问题。'
---

# `WebSpatialMixedEntryError`

同一个 JavaScript realm 同时加载 `@webspatial/react-sdk` 和 `@webspatial/react-sdk/eager` 时抛出。

## 结构

```ts
import { WebSpatialMixedEntryError } from "@webspatial/react-sdk";

type ReactSdkEntryKind = "lazy" | "eager";

class WebSpatialMixedEntryError extends Error {
  readonly previous: ReactSdkEntryKind;
  readonly next: ReactSdkEntryKind;
}
```

| 属性       | 含义                 |
| ---------- | -------------------- |
| `previous` | 首先注册的入口。     |
| `next`     | 后续加载的冲突入口。 |

整个应用 bundle（包括间接依赖）必须只选一个入口：

- Web-first、SSR、hydration 和渐进增强应用使用 `@webspatial/react-sdk`。
- 只在客户端渲染、只面向空间环境，并希望立即静态链接真实实现时，使用 `@webspatial/react-sdk/eager`。

```ts
// 支持：整个 bundle 统一使用一个入口。
import { Model, bootSpatial } from "@webspatial/react-sdk";

// 同一 bundle 中不支持：
// import { Reality } from "@webspatial/react-sdk/eager";
```

重复注册同一个入口是安全的。第一次混用报错后，同一页面生命周期内不会重复报告该冲突。

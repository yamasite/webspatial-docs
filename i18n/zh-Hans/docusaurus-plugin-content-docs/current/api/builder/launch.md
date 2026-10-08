---
sidebar_position: 5
description: '在 visionOS 模拟器中重新安装并启动之前准备好的 Builder 测试 App。'
---

# `webspatial-builder launch`

这是一个维护命令，用于从 Builder 临时测试目录启动 App。完整的构建并启动流程请优先使用 [`run`](./run.md)。

```bash
webspatial-builder launch [--bundle-id <id>]
```

`--bundle-id` 默认是 `com.webspatial.test`。命令会选择或创建 visionOS 模拟器，必要时启动模拟器，卸载同 Bundle ID 的旧 App，安装已有测试 App，再启动它。

:::caution[必须已有临时产物]
`launch` 不准备平台模板，也不构建 App。如果 Builder 临时测试目录中没有兼容 App，命令会失败。需要全新构建时运行 `webspatial-builder run`。
:::

---
sidebar_position: 6
description: "关闭 visionOS 模拟器，以及 Builder CLI 中保留命令 test 的说明。"
---

# `webspatial-builder shutdown`

用于关闭正在运行的 visionOS 模拟器。它是在调试会话之间清理模拟器的便捷命令，不会改动任何已构建的应用包。

## 语法 {#syntax}

```bash title="关闭模拟器"
webspatial-builder shutdown
```

`shutdown` 不接受任何选项。

## 保留命令：`test` {#test}

Builder CLI 还注册了 `test` 命令：

```bash
webspatial-builder test
```

它是为后续功能预留的命令，当前不执行任何操作。

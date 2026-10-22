---
sidebar_position: 5
description: "在 visionOS 模拟器中启动已安装好的 WebSpatial App。"
---

# `webspatial-builder launch`

用于启动 visionOS 模拟器中已经安装好的 WebSpatial App。与 [`run`](./run.md) 不同，它不会打包、安装或重建应用，只会按应用标识符启动已安装的应用。

## 语法 {#syntax}

```bash title="在模拟器中启动已安装的应用"
webspatial-builder launch [--bundle-id=<bundle id>]
```

## 选项 {#options}

### `--bundle-id`

选择在模拟器中启动的应用标识符。省略时 Builder 会使用默认测试标识符 `com.webspatial.test`。

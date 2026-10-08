---
sidebar_position: 6
description: '关闭 WebSpatial Builder 选中的 visionOS 模拟器。'
---

# `webspatial-builder shutdown`

```bash
webspatial-builder shutdown
```

查找 Builder 选中的第一台 visionOS 模拟器，并通过 `xcrun simctl` 关闭。如果模拟器已经关闭，命令只报告当前状态，不再修改。

该命令需要 macOS、Xcode 命令行工具和已安装的 visionOS 模拟器运行时。它不会停止 PICO 设备或 Web 开发服务器。

---
sidebar_position: 0
description: '根据预览、打包或发布任务选择正确的 WebSpatial Builder 命令。'
---

# WebSpatial Builder 命令

`@webspatial/builder` 提供 `webspatial-builder` CLI。发布包要求 Node.js 24 或更高版本。

| 命令                        | 状态     | 用途                                                     | 主要前置条件                                   |
| --------------------------- | -------- | -------------------------------------------------------- | ---------------------------------------------- |
| [`run`](./run.md)           | 公开     | 在 visionOS 模拟器中构建、安装并启动打包应用。           | macOS、Xcode 和 visionOS 模拟器运行时。        |
| [`run-pico`](./run-pico.md) | 公开     | 在已连接的 PICO OS 6 设备上打开托管的 WebSpatial 应用。  | `adb` 和一台已连接设备。                       |
| [`build`](./build.md)       | 公开     | 为真机测试生成已签名的 visionOS archive/export。         | macOS、Xcode、Apple Team ID 和 base URL/路径。 |
| [`publish`](./publish.md)   | 公开     | 构建并上传 visionOS 应用到 App Store Connect。           | 构建前置条件和上传凭证。                       |
| [`launch`](./launch.md)     | 维护     | 在 visionOS 模拟器中重新安装并启动之前准备好的测试 App。 | 已存在的 Builder 临时测试产物。                |
| [`shutdown`](./shutdown.md) | 维护     | 关闭 Builder 选中的第一台 visionOS 模拟器。              | macOS、Xcode 和 visionOS 模拟器。              |
| `test`                      | 内部占位 | 当前实现为空，不执行用户可见工作。                       | 不要用于应用工作流。                           |

运行 `webspatial-builder --help` 查看已注册命令；运行 `webspatial-builder <command> --help` 查看 Commander 层面的选项和必填要求。

:::caution[命令和选项区分大小写]
已实现的长选项包含 `--teamId`、`--buildType`、`--tryWithoutBuild` 等驼峰名称。请使用本文给出的拼写。
:::

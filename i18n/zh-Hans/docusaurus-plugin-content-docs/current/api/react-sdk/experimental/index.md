---
sidebar_position: 1
description: '来自 @webspatial/react-sdk/experimental 的可选预发布 API：Ornament、WebSpatialInspector、useAnimation 和 useEntityAnimation。'
---

# 实验性 API

## 概述 {#summary}

实验性 API 通过子路径 `@webspatial/react-sdk/experimental` 发布：

```js
import {
  Ornament,
  WebSpatialInspector,
  useAnimation,
  useEntityAnimation,
} from "@webspatial/react-sdk/experimental";
```

这些 API 是显式可选的。在“毕业”进入默认入口之前，它们的名称、参数和属性都可能变化——采用时请锁定包版本。

:::note[`/spatial` 不是面向应用的导入]
这个包还暴露了 `@webspatial/react-sdk/spatial` 子路径，但它只是运行时内部调用的 bridge 专用动态 import 目标。应用代码绝不应直接导入它。下面的运动 Hook 之所以有文档，是因为它们同时通过面向应用的 `/experimental` 入口发布。
:::

## 已发布导出 {#available-exports}

| 导出                  | 类型                 | 页面                                          |
| --------------------- | -------------------- | --------------------------------------------- |
| `Ornament`            | 组件                 | [Ornament](./Ornament.md)                     |
| `WebSpatialInspector` | 组件（调试浮层）     | [Ornament 的兄弟浮层](#webspatialinspector)   |
| `useAnimation`        | Hook——空间化容器动画 | [useAnimation](./useAnimation.md)             |
| `useEntityAnimation`  | Hook——实体动画       | [useEntityAnimation](./useEntityAnimation.md) |

该入口还发布配套类型：`OrnamentProps`、`OrnamentPoint3D`、`OrnamentVisibility`、`WebSpatialInspectorProps`、`UseAnimationResult`、`SpatializedMotionConfig`、`SpatializedMotionSegmentConfig`、`SpatializedPlaybackApi`、`EntityMotionConfig`、`EntityMotionProps`、`EntityPlaybackApi`、`EntityPlaybackError`、`EntityMotionAnimation` 和 `UseEntityAnimationResult`。

## 共同行为约定 {#shared-behavioral-contract}

实验性组件和 Hook 与 SDK 其余部分共享同一套运行时门控行为：

- Hook（`useAnimation`、`useEntityAnimation`）在 [`bootSpatial()`](../js-api/bootSpatial.md) 完成前会抛出命名对应能力的 [`WebSpatialRuntimeError`](../js-api/WebSpatialRuntime.md#related-errors)。把带动画的子树挂在 [`<SpatialBoot>`](../react-components/SpatialBoot.md) 内部。
- 当 `WebSpatialRuntime.supports()` 报告当前运行时不支持该能力时，组件渲染 `null`；boot 被遗忘时渲染开发环境诊断信息。

## WebSpatialInspector {#webspatialinspector}

`WebSpatialInspector` 是实验性调试浮层（基于 ornament 的面板），用于检视正在运行的空间场景。它与 `Ornament` 使用相同的运行时支持与就绪检查门控。

| 属性               | 类型              | 说明                                 |
| ------------------ | ----------------- | ------------------------------------ |
| `editable`         | `boolean`         | 可选。                               |
| `width`            | `number`          | 可选。                               |
| `height`           | `number`          | 可选。                               |
| `attachmentAnchor` | `OrnamentPoint3D` | 可选；与 `Ornament` 的锚点类型相同。 |
| `contentAlignment` | `OrnamentPoint3D` | 可选；与 `Ornament` 的对齐类型相同。 |

基于控制台的检视工具见[调试空间场景](../../../how-to/debug.md)。

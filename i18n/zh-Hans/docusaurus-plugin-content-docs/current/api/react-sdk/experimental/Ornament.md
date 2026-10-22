---
sidebar_position: 2
description: '把 2D 内容悬浮锚定在空间中，来自实验性入口。'
---

# `<Ornament>`

## 概述 {#summary}

`Ornament` 把你的 2D 子内容渲染成一个悬浮在空间中的表面，独立于页面的空间化容器。它从实验性入口导出：

```jsx
import { Ornament } from "@webspatial/react-sdk/experimental";
```

你的子内容渲染在 ornament 自己的子窗口中，因此其中可以正常使用 HTML、CSS 和布局。

## 属性 {#props}

| 属性               | 类型                                                                        | 说明                                                              |
| ------------------ | --------------------------------------------------------------------------- | ----------------------------------------------------------------- |
| `attachmentAnchor` | `OrnamentPoint3D`                                                           | 可选。ornament 的附着点。                                         |
| `contentAlignment` | `OrnamentPoint3D`                                                           | 可选。内容在 ornament 内部的对齐方式。                            |
| `visibility`       | `OrnamentVisibility`                                                        | 可选。                                                            |
| `width`            | `number`                                                                    | 可选。                                                            |
| `height`           | `number`                                                                    | 可选。                                                            |
| `cornerRadius`     | `Partial<CornerRadius>`                                                     | 可选。逐角半径覆盖。                                              |
| `children`         | `ReactNode`                                                                 | 必填。要悬浮的 2D 内容。                                          |
| `style`            | `CSSProperties` & `{ "--xr-background-material"?: BackgroundMaterialType }` | 可选。应用到 ornament 子窗口的样式；该 CSS 变量用于选择背景材质。 |

## 行为 {#behavior}

- **能力门控。** 当 `WebSpatialRuntime.supports("Ornament")` 为 `false` 时，组件渲染 `null`。
- **boot 门控。** 空间运行时尚未完成启动时，组件渲染一条开发诊断，提醒你把该子树包在 [`<SpatialBoot>`](../react-components/SpatialBoot.md) 中。
- **禁止嵌套。** `Ornament` 不能用在另一个 ornament 的内容内部，也不能用在 attachment 的内容内部；这种用法会打印开发警告且不渲染任何内容。
- 挂载期间属性变化会直接应用到运行中的 ornament，无需重建。

## 示例 {#example}

```jsx
import { Ornament } from "@webspatial/react-sdk/experimental";
import { SpatialBoot } from "@webspatial/react-sdk";

export function HelpCard() {
  return (
    <SpatialBoot>
      <Ornament width={400} height={200} style={{ "--xr-background-material": "translucent" }}>
        <h2>需要帮助？</h2>
        <p>这张卡片悬浮在空间中，独立于主页面。</p>
      </Ornament>
    </SpatialBoot>
  );
}
```

## 相关 API {#related-apis}

- [实验性 API 索引](./index.md)——入口约定与共同门控行为。
- [`WebSpatialRuntime.supports`](../js-api/WebSpatialRuntime.md)——`Ornament` 门控背后的探测函数。
- [调试空间场景](../../../how-to/debug.md)——在运行时检视 ornament 及其他场景对象。

---
sidebar_position: 4
description: '查询当前运行时是否支持特定的 WebSpatial 组件、API、事件和选项。'
---

# `WebSpatialRuntime.supports`

## 概述 {#summary}

检查当前运行时是否支持某项 WebSpatial 能力。需要按功能决定是否执行代码时，应使用此 API，而不是通过 `navigator.userAgent` 推断支持情况，也不要先调用 API 再捕获错误。

该方法同步返回结果；在同一个页面生命周期内，同一运行时状态的查询结果保持稳定。

## 调用形式 {#signature}

```ts
import { WebSpatialRuntime } from "@webspatial/react-sdk";

const supported = WebSpatialRuntime.supports(name, tokens?);
```

```ts
type Supports = (name: string, tokens?: readonly string[]) => boolean;
```

## 参数 {#parameters}

### `name`

已公开的顶层能力键。传入未知名称时返回 `false`。

### `tokens`

可选的子能力 token 数组，token 必须属于 `name`。

- 多个 token 使用 **AND 语义**：只有全部受支持时才返回 `true`。
- 空数组与省略 `tokens` 的行为一致。
- 未知 token，或不属于该 `name` 的 token，返回 `false`。

```ts
WebSpatialRuntime.supports("Model");
WebSpatialRuntime.supports("Model", ["poster", "play"]);
WebSpatialRuntime.supports("VolumeScene", [
  "worldScaling",
  "baseplateVisibility",
]);
```

## 返回值 {#return-value}

当前运行时支持完整查询时返回 `true`，否则返回 `false`。

在普通浏览器和服务端渲染期间，空间能力返回 `false`。即使 `window` 或 `navigator` 不可用，调用 `supports` 也不会仅因此抛出异常。

## 能力键 {#capability-keys}

| 分组         | 顶层能力键                                                                                                                                                                                                                                         |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 组件与资源   | `Ornament`、`Model`、`Reality`、`Entity`、`BoxEntity`、`SphereEntity`、`ConeEntity`、`CylinderEntity`、`PlaneEntity`、`SceneGraph`、`ModelAsset`、`ModelEntity`、`UnlitMaterial`、`PBRMaterial`、`Material`、`AttachmentAsset`、`AttachmentEntity` |
| CSS          | `-xr-background-material`、`-xr-back`、`-xr-depth`、`-xr-transform`                                                                                                                                                                                |
| 事件         | `SpatialTapEvent`、`SpatialDragStartEvent`、`SpatialDragEvent`、`SpatialDragEndEvent`、`SpatialRotateEvent`、`SpatialRotateEndEvent`、`SpatialMagnifyEvent`、`SpatialMagnifyEndEvent`                                                              |
| 动画         | `useAnimation`、`useEntityAnimation`                                                                                                                                                                                                               |
| JS 与场景    | `useMetrics`、`convertCoordinate`、`initScene`、`WindowScene`、`VolumeScene`                                                                                                                                                                       |
| DOM 深度回读 | `xrClientDepth`、`xrOffsetBack`、`xrInnerDepth`、`xrOuterDepth`                                                                                                                                                                                    |

别名 `Box`、`Sphere`、`Cone`、`Cylinder`、`Plane` 和 `World` 会分别规范化为 `BoxEntity`、`SphereEntity`、`ConeEntity`、`CylinderEntity`、`PlaneEntity` 和 `SceneGraph`。新代码应优先使用规范名称。

## 子能力 Token {#sub-capability-tokens}

| 顶层名称             | 支持的 token                                                                                                                                                                   |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `Material`           | `unlit`、`pbr`                                                                                                                                                                 |
| `WindowScene`        | `defaultSize`、`resizability`                                                                                                                                                  |
| `VolumeScene`        | `defaultSize`、`resizability`、`worldScaling`、`worldAlignment`、`baseplateVisibility`                                                                                         |
| `SpatialRotateEvent` | `constrainedToAxis`                                                                                                                                                            |
| `AttachmentEntity`   | `placement`                                                                                                                                                                    |
| `Model`              | `autoplay`、`loop`、`stagemode`、`poster`、`loading`、`source`、`ready`、`currentSrc`、`entityTransform`、`paused`、`duration`、`playbackRate`、`play`、`pause`、`currentTime` |

本表未列出的能力只能做顶层查询；向它们传入 token 会返回 `false`。

## 解析与回退行为 {#resolution-and-fallback-behavior}

SDK 以保守方式解析能力查询：

1. 检测到的运行时若提供有效能力清单，该清单具有最高优先级。它是完整 allowlist，已知能力未列出即表示不支持。
2. 没有可用能力清单时，SDK 回退到 shell 版本能力表。
3. 未知运行时、格式错误的版本、未知能力键或无效 token 都返回 `false`。

:::caution[`Model` 有原生回退]
当 `WebSpatialRuntime.supports("Model")` 返回 `false` 时，React SDK 仍可渲染原生 `<model>` 元素并透传属性。该结果表示 **WebSpatial runtime 实现**的 `Model` 是否可用，并不意味着所有浏览器都不支持原生 `<model>`。
:::

:::note[Puppeteer 测试环境]
WebSpatial 的 Puppeteer 测试环境会对所有有效能力查询返回 `true`，以便端到端测试覆盖空间代码路径。不要把这一测试专用行为视为产品支持矩阵。
:::

## 示例 {#example}

```tsx
import { Model, WebSpatialRuntime } from "@webspatial/react-sdk";

const supportsPosterPlayback = WebSpatialRuntime.supports("Model", [
  "poster",
  "play",
]);

export function ProductPreview() {
  if (!supportsPosterPlayback) {
    return <img src="/product-poster.png" alt="商品预览" />;
  }

  return <Model src="/product.usdz" poster="/product-poster.png" />;
}
```

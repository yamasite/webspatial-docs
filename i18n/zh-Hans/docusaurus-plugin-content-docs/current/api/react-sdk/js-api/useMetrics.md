---
sidebar_position: 2
description: '在 WebSpatial 布局中完成 2D 像素单位和真实世界米单位之间的换算。'
---

# `useMetrics`

## 概述 {#summary}

在 2D GUI 使用的 point 单位（`px`）和 3D 空间使用的物理世界单位（`m`）之间做[单位转换](../../../concepts/3d-content-containers.md#2d-containing-3d)。

在 visionOS 里，默认约 `1360px ≈ 1 米`，但这个换算并不固定：`window` 场景、以及 [`worldScaling`](../scene-options/worldScaling.md) 为 `dynamic` 的 `volume` 场景都会改变它——见 [`worldScalingCompensation`](#return-shape)。不同空间计算平台的换算关系也不同，因此请统一通过本 API 换算，不要硬编码像素。

:::caution[只能在 WebSpatial 环境中使用]
`useMetrics` 本身是 SSR 安全的，调用时不会抛错；但返回的换算函数只有在 WebSpatial 环境中、SDK 空间能力加载完成后才可用。在普通网页、SSR 期间，或启动完成之前调用 `pointToPhysical`/`physicalToPoint` 都会抛出 `WebSpatialRuntimeError`。请把调用它的组件放在 [`<SpatialBoot>`](../react-components/SpatialBoot.md) 内部，并且只在空间平台上渲染这些组件，例如借助[运行时检测](../dom-api/userAgent.md)。
:::

## 调用形式 {#signature}

```js
import { useMetrics } from "@webspatial/react-sdk";

function UnitConvertTest() {
  const { pointToPhysical, physicalToPoint } = useMetrics();

  return (
    <>
      <pre>
        Scaled conversion
        {"\n"}
        physicalToPoint(1): {physicalToPoint(1)}
        {"\n"}
        pointToPhysical(1): {pointToPhysical(1)}
      </pre>

      <pre>
        Unscaled conversion
        {"\n"}
        physicalToPoint(1):{" "}
        {physicalToPoint(1, { worldScalingCompensation: "unscaled" })}
        {"\n"}
        pointToPhysical(1):{" "}
        {pointToPhysical(1, { worldScalingCompensation: "unscaled" })}
      </pre>
    </>
  );
}
```

## 参数 {#parameters}

无

## 返回结构 {#return-shape}

```ts
type WorldScalingCompensation = "scaled" | "unscaled";

type MetricConvertOptions = {
  worldScalingCompensation?: WorldScalingCompensation;
};

type UseMetricsReturn = {
  pointToPhysical: (value: number, options?: MetricConvertOptions) => number;
  physicalToPoint: (value: number, options?: MetricConvertOptions) => number;
};
```

`worldScalingCompensation` 决定在转换过程中，是否对当前空间场景容器的 `worldScaling` 进行补偿。

- `scaled`：转换结果与经过 `worldScaling` 自动缩放后用户感知到的尺寸一致
- `unscaled`：得到不随 `worldScaling` 缩放而变化的稳定物理世界数值

### pointToPhysical

```ts
pointToPhysical(value: number, options?: MetricConvertOptions): number
```

### physicalToPoint

```ts
physicalToPoint(value: number, options?: MetricConvertOptions): number
```

## 生命周期：占位实现 vs 真实实现 {#lifecycle}

组件实例拿到真实实现还是网页占位实现，是在**首次挂载时一次性决定**的，取决于当时空间运行时是否已就绪：

- 如果运行时已就绪，该实例拿到真实的 `useMetrics`。
- 否则该实例拿到一个冻结的占位实现，其 `pointToPhysical`/`physicalToPoint` 总会抛出 `WebSpatialRuntimeError`；这两个函数引用在多次渲染间保持稳定，可以安全地放进 `useEffect` 依赖数组。

这个选择在实例整个生命周期内不会切换：即使之后 `bootSpatial()` 完成，挂载时拿到占位实现的组件仍然使用占位实现。要升级到真实实现，必须把组件**卸载后重新挂载**——例如改变它的 `key`、卸载父组件，或刷新页面。

```jsx
// 启动完成后强制重挂载，让占位实现升级为真实 hook。
<SpatialReadyKeyedDiv key={booted ? "real" : "placeholder"} />
```

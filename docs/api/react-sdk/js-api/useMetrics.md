---
sidebar_position: 2
description: 'Convert between 2D pixel units and real-world meter units inside WebSpatial layouts.'
---

# `useMetrics`

## Summary

Performs [unit conversion](../../../concepts/3d-content-containers.md#2d-containing-3d) between the point unit (`px`) used by 2D GUI and the physical world unit (`m`) used by 3D space.

On visionOS, the default mapping is roughly `1360px ≈ 1 meter`, but it is not fixed: it shifts for `window` scenes and for `volume` scenes whose [`worldScaling`](../scene-options/worldScaling.md) is `dynamic` — see [`worldScalingCompensation`](#return-shape). The ratio also differs across spatial computing platforms, so convert with this API rather than hard-coding pixels.

:::caution[Only works in a WebSpatial environment]
`useMetrics` itself is SSR-safe and never throws when called, but the returned conversion functions only work after the spatial capabilities of the SDK have finished loading in a WebSpatial environment. Invoking `pointToPhysical`/`physicalToPoint` in plain web, during SSR, or before boot resolves throws a `WebSpatialRuntimeError`. Put components that call it inside [`<SpatialBoot>`](../react-components/SpatialBoot.md), and render them only on spatial platforms, for example with [runtime detection](../dom-api/userAgent.md).
:::

## Signature

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

## Parameters

None.

## Return Shape

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

`worldScalingCompensation` determines whether the current Spatial Scene container's `worldScaling` should be compensated during the conversion.

- `scaled`: the conversion result matches the size perceived by the user after automatic scaling from `worldScaling`
- `unscaled`: returns a stable physical-world value that does not change with `worldScaling`

### pointToPhysical

```ts
pointToPhysical(value: number, options?: MetricConvertOptions): number
```

### physicalToPoint

```ts
physicalToPoint(value: number, options?: MetricConvertOptions): number
```

## Lifecycle: placeholder vs. real implementation {#lifecycle}

Whether a component instance gets the real implementation or the web placeholder is decided **once, when the component first mounts**, based on whether the spatial runtime is ready at that moment:

- If the runtime is already ready, the instance gets the real `useMetrics`.
- Otherwise the instance gets a frozen placeholder whose `pointToPhysical`/`physicalToPoint` always throw a `WebSpatialRuntimeError`. The two function references are stable across renders, so they are safe to use in `useEffect` dependency arrays.

The choice never switches during the instance's lifetime: a component that mounted the placeholder keeps using it even after `bootSpatial()` later resolves. To pick up the real implementation, the component **must be unmounted and remounted** — for example by changing its `key`, unmounting its parent, or reloading the page.

```jsx
// After boot resolves, force remount so the placeholder upgrades to the real hook.
<SpatialReadyKeyedDiv key={booted ? "real" : "placeholder"} />
```

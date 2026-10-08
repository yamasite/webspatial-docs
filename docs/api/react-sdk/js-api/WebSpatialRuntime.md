---
sidebar_position: 4
description: 'Query whether the current runtime supports specific WebSpatial components, APIs, events, and options.'
---

# `WebSpatialRuntime.supports`

## Summary

Checks whether the current runtime supports a WebSpatial capability. Use this API to gate feature-specific code instead of inferring support from `navigator.userAgent` or trying an API and catching failures.

The result is synchronous and remains stable for the lifetime of the page.

## Signature

```ts
import { WebSpatialRuntime } from "@webspatial/react-sdk";

const supported = WebSpatialRuntime.supports(name, tokens?);
```

```ts
type Supports = (name: string, tokens?: readonly string[]) => boolean;
```

## Parameters

### `name`

A documented top-level capability key. Unknown names return `false`.

### `tokens`

An optional array of documented sub-capability tokens for `name`.

- All tokens use **AND semantics**: the result is `true` only when every token is supported.
- An empty array behaves like omitting `tokens`.
- An unknown token, or a token that does not belong to `name`, returns `false`.

```ts
WebSpatialRuntime.supports("Model");
WebSpatialRuntime.supports("Model", ["poster", "play"]);
WebSpatialRuntime.supports("VolumeScene", [
  "worldScaling",
  "baseplateVisibility",
]);
```

## Return Value

Returns `true` when the current runtime supports the complete query; otherwise returns `false`.

In plain browsers and during server-side rendering, spatial capabilities return `false`. Calling `supports` does not throw solely because `window` or `navigator` is unavailable.

## Capability Keys

| Group                 | Top-level capability keys                                                                                                                                                                                                                          |
| --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Components and assets | `Ornament`, `Model`, `Reality`, `Entity`, `BoxEntity`, `SphereEntity`, `ConeEntity`, `CylinderEntity`, `PlaneEntity`, `SceneGraph`, `ModelAsset`, `ModelEntity`, `UnlitMaterial`, `PBRMaterial`, `Material`, `AttachmentAsset`, `AttachmentEntity` |
| CSS                   | `-xr-background-material`, `-xr-back`, `-xr-depth`, `-xr-transform`                                                                                                                                                                                |
| Events                | `SpatialTapEvent`, `SpatialDragStartEvent`, `SpatialDragEvent`, `SpatialDragEndEvent`, `SpatialRotateEvent`, `SpatialRotateEndEvent`, `SpatialMagnifyEvent`, `SpatialMagnifyEndEvent`                                                              |
| Animation             | `useAnimation`, `useEntityAnimation`                                                                                                                                                                                                               |
| JS and scenes         | `useMetrics`, `convertCoordinate`, `initScene`, `WindowScene`, `VolumeScene`                                                                                                                                                                       |
| DOM depth readbacks   | `xrClientDepth`, `xrOffsetBack`, `xrInnerDepth`, `xrOuterDepth`                                                                                                                                                                                    |

The aliases `Box`, `Sphere`, `Cone`, `Cylinder`, `Plane`, and `World` are normalized to `BoxEntity`, `SphereEntity`, `ConeEntity`, `CylinderEntity`, `PlaneEntity`, and `SceneGraph` respectively. Prefer the canonical names in new code.

## Sub-capability Tokens

| Top-level name       | Supported tokens                                                                                                                                                               |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `Material`           | `unlit`, `pbr`                                                                                                                                                                 |
| `WindowScene`        | `defaultSize`, `resizability`                                                                                                                                                  |
| `VolumeScene`        | `defaultSize`, `resizability`, `worldScaling`, `worldAlignment`, `baseplateVisibility`                                                                                         |
| `SpatialRotateEvent` | `constrainedToAxis`                                                                                                                                                            |
| `AttachmentEntity`   | `placement`                                                                                                                                                                    |
| `Model`              | `autoplay`, `loop`, `stagemode`, `poster`, `loading`, `source`, `ready`, `currentSrc`, `entityTransform`, `paused`, `duration`, `playbackRate`, `play`, `pause`, `currentTime` |

Capabilities not listed in this table are top-level-only checks. Passing tokens to them returns `false`.

## Resolution and Fallback Behavior

The SDK resolves capability queries conservatively:

1. A valid capability manifest from the detected runtime is authoritative. It is a complete allowlist, so an omitted known capability is unsupported.
2. If no usable manifest is available, the SDK falls back to its shell-version capability table.
3. An unknown runtime, malformed version, unknown key, or invalid token returns `false`.

:::caution[`Model` has a native fallback]
When `WebSpatialRuntime.supports("Model")` is `false`, the React SDK can still render a native `<model>` element with props passthrough. The result indicates whether the **WebSpatial runtime implementation** of `Model` is available; it does not mean that every browser lacks native `<model>` support.
:::

:::note[Puppeteer test harness]
The WebSpatial Puppeteer harness reports `true` for every valid capability query so end-to-end tests exercise spatial code paths. Do not treat that test-only behavior as a product support matrix.
:::

## Example

```tsx
import { Model, WebSpatialRuntime } from "@webspatial/react-sdk";

const supportsPosterPlayback = WebSpatialRuntime.supports("Model", [
  "poster",
  "play",
]);

export function ProductPreview() {
  if (!supportsPosterPlayback) {
    return <img src="/product-poster.png" alt="Product preview" />;
  }

  return <Model src="/product.usdz" poster="/product-poster.png" />;
}
```

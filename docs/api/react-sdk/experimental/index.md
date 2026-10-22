---
sidebar_position: 1
description: 'Opt-in pre-release APIs from @webspatial/react-sdk/experimental: Ornament, WebSpatialInspector, useAnimation, and useEntityAnimation.'
---

# Experimental APIs

## Summary

Experimental APIs are published from the subpath `@webspatial/react-sdk/experimental`:

```js
import {
  Ornament,
  WebSpatialInspector,
  useAnimation,
  useEntityAnimation,
} from "@webspatial/react-sdk/experimental";
```

These APIs are intentionally opt-in. Their names, parameters, and props may change before they graduate into the default entry — pin your package version when you adopt them.

:::note[`/spatial` is not an app-facing import]
The package also exposes a `@webspatial/react-sdk/spatial` subpath, but it is a bridge-only dynamic-import target invoked internally by the runtime. Application code must never import it directly. The motion hooks below are documented because they are also published through the app-facing `/experimental` entry.
:::

## Available exports

| Export                | Kind                                | Page                                               |
| --------------------- | ----------------------------------- | -------------------------------------------------- |
| `Ornament`            | Component                           | [Ornament](./Ornament.md)                          |
| `WebSpatialInspector` | Component (debug overlay)           | [Ornament's sibling overlay](#webspatialinspector) |
| `useAnimation`        | Hook — spatialized container motion | [useAnimation](./useAnimation.md)                  |
| `useEntityAnimation`  | Hook — Entity motion                | [useEntityAnimation](./useEntityAnimation.md)      |

The entry also publishes their supporting types: `OrnamentProps`, `OrnamentPoint3D`, `OrnamentVisibility`, `WebSpatialInspectorProps`, `UseAnimationResult`, `SpatializedMotionConfig`, `SpatializedMotionSegmentConfig`, `SpatializedPlaybackApi`, `EntityMotionConfig`, `EntityMotionProps`, `EntityPlaybackApi`, `EntityPlaybackError`, `EntityMotionAnimation`, and `UseEntityAnimationResult`.

## Shared behavioral contract

Experimental components and hooks share the runtime-gating behavior of the rest of the SDK:

- Hooks (`useAnimation`, `useEntityAnimation`) throw a [`WebSpatialRuntimeError`](../js-api/WebSpatialRuntime.md#related-errors) naming the capability until [`bootSpatial()`](../js-api/bootSpatial.md) has resolved. Mount the animated subtree inside [`<SpatialBoot>`](../react-components/SpatialBoot.md).
- Components render `null` when `WebSpatialRuntime.supports()` reports the capability as unsupported on the current runtime, and show a development diagnostic when boot has been forgotten.

## WebSpatialInspector

`WebSpatialInspector` is an experimental debug overlay (an ornament-based panel) for inspecting the live spatial scene. It gates on the same runtime support and readiness checks as `Ornament`.

| Prop               | Type              | Description                                  |
| ------------------ | ----------------- | -------------------------------------------- |
| `editable`         | `boolean`         | Optional.                                    |
| `width`            | `number`          | Optional.                                    |
| `height`           | `number`          | Optional.                                    |
| `attachmentAnchor` | `OrnamentPoint3D` | Optional; same anchor type as `Ornament`.    |
| `contentAlignment` | `OrnamentPoint3D` | Optional; same alignment type as `Ornament`. |

For console-based inspection utilities, see [Debug the spatial scene](../../../how-to/debug.md).

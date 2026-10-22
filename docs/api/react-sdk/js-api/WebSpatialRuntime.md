---
sidebar_position: 5
description: 'Probe which spatial capabilities the current runtime supports, synchronously and without user-agent parsing.'
---

# `WebSpatialRuntime`

## Summary

`WebSpatialRuntime` is a small namespace exported from the default entry that exposes a synchronous capability probe. Use it to gate spatial features per runtime, instead of writing your own [user-agent detection](../dom-api/userAgent.md).

```js
import { WebSpatialRuntime } from "@webspatial/react-sdk";

if (WebSpatialRuntime.supports("Ornament")) {
  // Render the experimental ornament UI.
}
```

The probe resolves capabilities from the detected runtime (visionOS, PicoOS, or a Puppeteer test harness). In a plain browser, on the server, or before any spatial runtime is detectable, every probe returns `false`.

## Signature

```ts
WebSpatialRuntime.supports(name: string, tokens?: readonly string[]): boolean;
```

## Parameters

### name

The capability key to probe. Unknown names (anything not listed in [Capability keys](#capability-keys)) return `false` instead of throwing. A few legacy component aliases are normalized before lookup: `Box` → `BoxEntity`, `Sphere` → `SphereEntity`, `Cone` → `ConeEntity`, `Cylinder` → `CylinderEntity`, `Plane` → `PlaneEntity`, `World` → `SceneGraph`.

### tokens

Optional sub-tokens that refine the probe. Every sub-token must be a documented sub-token for `name`; if any token is unknown, non-string, or unsupported by the runtime, the result is `false`. Sub-tokens are combined with AND semantics — all of them must be supported.

## Return

`boolean`.

- `true` — the runtime supports the capability (and every requested sub-token).
- `false` — the runtime does not support it, `name` is unknown, or no spatial runtime is detectable at all.

Results are consistent across repeated reads during a page lifetime; capabilities are treated as stable once the runtime is determined (there is no subscribe or mid-session refresh API).

## Capability keys

| Group                    | Keys                                                                                                                                                                                                                                               |
| ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Components               | `Ornament`, `Model`, `Reality`, `Entity`, `BoxEntity`, `SphereEntity`, `ConeEntity`, `CylinderEntity`, `PlaneEntity`, `SceneGraph`, `ModelAsset`, `ModelEntity`, `UnlitMaterial`, `PBRMaterial`, `Material`, `AttachmentAsset`, `AttachmentEntity` |
| CSS APIs                 | `-xr-background-material`, `-xr-back`, `-xr-depth`, `-xr-transform`                                                                                                                                                                                |
| Spatial gesture events   | `SpatialTapEvent`, `SpatialDragStartEvent`, `SpatialDragEvent`, `SpatialDragEndEvent`, `SpatialRotateEvent`, `SpatialRotateEndEvent`, `SpatialMagnifyEvent`, `SpatialMagnifyEndEvent`                                                              |
| Animation (experimental) | `useAnimation`, `useEntityAnimation`                                                                                                                                                                                                               |
| JS & scene APIs          | `useMetrics`, `convertCoordinate`, `initScene`, `WindowScene`, `VolumeScene`                                                                                                                                                                       |
| DOM readbacks            | `xrClientDepth`, `xrOffsetBack`, `xrInnerDepth`, `xrOuterDepth`                                                                                                                                                                                    |

## Known sub-tokens

| `name`               | Documented sub-tokens                                                                                                                                                                                                     |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Material`           | `unlit`, `pbr`                                                                                                                                                                                                            |
| `WindowScene`        | `defaultSize`, `resizability`                                                                                                                                                                                             |
| `VolumeScene`        | `defaultSize`, `resizability`, `worldScaling`, `worldAlignment`, `baseplateVisibility`                                                                                                                                    |
| `SpatialRotateEvent` | `constrainedToAxis`                                                                                                                                                                                                       |
| `AttachmentEntity`   | `placement`                                                                                                                                                                                                               |
| `Model`              | `autoplay`, `loop`, `stagemode`, `poster`, `loading`, `source`, `ready`, `currentSrc`, `boundingBoxCenter`, `boundingBoxExtents`, `entityTransform`, `paused`, `duration`, `playbackRate`, `play`, `pause`, `currentTime` |

## Types

The default entry also exports the matching TypeScript types:

```ts
type CapabilityKey;
type WebSpatialRuntimeType = "visionos" | "picoos" | "puppeteer" | null;
type WebSpatialRuntimeSnapshot = {
  type: WebSpatialRuntimeType;
  shellVersion: string | null;
};
```

`WebSpatialRuntimeSnapshot` describes the runtime classification that backs `supports()`. It exists so application code can type its own runtime-specific integrations; the probe itself is the documented entry point.

## Behavior by runtime

- **Plain browser / SSR** — no runtime is detectable, so `supports()` returns `false` for every key. No dynamic import is scheduled by probing.
- **Puppeteer test harness** — treated as a spatial-runtime equivalent: `supports()` returns `true` for any documented capability key, so end-to-end tests exercise the real spatial chunk.
- **visionOS / PicoOS** — capabilities resolve against the runtime's declared shell version. An unknown or unparseable shell version makes the probe return `false`.

## Related errors

Runtime-gated APIs that are called when `supports()` is `false` — or before [`bootSpatial()`](bootSpatial.md) has resolved — throw `WebSpatialRuntimeError`, an `Error` subclass that exposes the rejected `capability: string`. See [useMetrics](useMetrics.md) and [convertCoordinate](convertCoordinate.md) for the per-API fallback contracts, and the [experimental animation pages](../experimental/useAnimation.md) for the boot-gated variant.

## Examples

Gate a feature on a component capability:

```jsx
{WebSpatialRuntime.supports("Ornament") && <Ornament>...</Ornament>}
```

Probe a sub-token:

```js
const canConstrainRotation = WebSpatialRuntime.supports("SpatialRotateEvent", [
  "constrainedToAxis",
]);
```

Guard a DOM readback before reading it:

```js
if (WebSpatialRuntime.supports("xrOuterDepth")) {
  console.log(window.xrOuterDepth);
}
```

## Related APIs

- [`bootSpatial`](bootSpatial.md) — loads the spatial chunk that most gated APIs depend on.
- [`userAgent`](../dom-api/userAgent.md) — lower-level runtime detection this probe builds on.
- [Experimental APIs](../experimental/index.md) — `Ornament`, `useAnimation`, and `useEntityAnimation` are all capability-gated.

<!--
sidebar_position: 3
description: 'Animate spatialized 2D and 3D containers along a declarative timeline, from the experimental entry.'
-->

# `useAnimation()`

## Summary

`useAnimation` declares a timeline animation for a [spatialized 2D or 3D container](../react-components/Model.md) (for example `<Model>`). It returns an opaque binding you hand to the container's `xr-animation` prop, a playback API, and a React style outlet for the current values.

```jsx
import { useAnimation } from "@webspatial/react-sdk/experimental";
```

> [!CAUTION] > **Boot-gated**
>
> `useAnimation` throws a `WebSpatialRuntimeError` naming `useAnimation` until [`bootSpatial()`](../js-api/bootSpatial.md) has resolved. Mount the animated subtree inside [`<SpatialBoot>`](../react-components/SpatialBoot.md).

## Signature

```ts
useAnimation(config: SpatializedMotionConfig): UseAnimationResult;
```

```ts
type UseAnimationResult = readonly [
  animation: SpatializedMotionBinding,
  api: SpatializedPlaybackApi,
  style: CSSProperties,
];
```

## Config

`SpatializedMotionConfig` accepts either a single segment or a percentage-keyframe timeline:

```ts
// Segment: from → to
{
  from: { opacity: 1, transform: { translate: { x: 0 } } },
  to: { opacity: 0.2, transform: { translate: { x: 120 } } },
  duration: 0.6,          // seconds
  timingFunction: "easeInOut", // linear | easeIn | easeOut | easeInOut
  delay: 0,
  autoStart: true,
  loop: false,            // or true, or { reverse: true }
  playbackRate: 1,
  onStart, onComplete, onStop, onReset, onError,
}

// Timeline: percentage-keyframe record
{
  duration: 0.6,
  timeline: {
    "0%":   { opacity: 1, transform: { translate: { x: 0 } } },
    "100%": { opacity: 0.2, transform: { translate: { x: 120 } } },
  },
}
```

Animatable visual values: `opacity`, and `transform.translate` / `transform.rotate` / `transform.scale` per axis (translate in CSS pixels, rotation in degrees, scale unitless — aligned with the CSS equivalents). Arbitrary CSS transform strings, skew, perspective, and matrices are not interpolated.

## Return

### animation (binding)

An opaque binding object. Pass it to the container's `xr-animation` prop:

```jsx
<Model src={modelUrl} xr-animation={animation} style={style} />
```

In a plain browser, the fallback host is rendered without the binding — the page degrades to static HTML.

### api

```ts
interface SpatializedPlaybackApi {
  play(): void;
  pause(): void;
  stop(): void;
  reset(): void;
  finish(): void;
  readonly isAnimating: boolean;
  readonly isPaused: boolean;
  readonly finished: boolean;
  readonly playState: "idle" | "queued" | "running" | "paused" | "finished";
}
```

### style

A React `CSSProperties` outlet derived from the animation's current visual values. Spread it onto the container so intermediate values also render on the web fallback host.

## Example

```jsx
import { SpatialBoot } from "@webspatial/react-sdk";
import { Model } from "@webspatial/react-sdk";
import { useAnimation } from "@webspatial/react-sdk/experimental";

function AnimatedModel() {
  const [animation, api, style] = useAnimation({
    from: { transform: { translate: { z: 0 } } },
    to: { transform: { translate: { z: 200 } } },
    duration: 1.2,
    loop: { reverse: true },
  });

  return (
    <div>
      <button onClick={() => api.pause()}>Pause</button>
      <Model src="/models/chair.glb" xr-animation={animation} style={style} />
    </div>
  );
}
```

## Related APIs

- [`useEntityAnimation`](./useEntityAnimation.md) — timeline motion for 3D entities.
- [Experimental index](./index.md) — entry contract and shared gating behavior.
- [`<Model>`](../react-components/Model.md) — the container that consumes `xr-animation`.

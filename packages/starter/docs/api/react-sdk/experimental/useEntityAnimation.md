<!--
sidebar_position: 4
description: 'Animate 3D entities along a declarative transform timeline, from the experimental entry.'
-->

# `useEntityAnimation()`

## Summary

`useEntityAnimation` declares a transform timeline animation for a 3D [Entity](../react-components/Reality.md#3d-entity). It returns an animation handle you spread onto the entity's `animation` prop, a playback API, and the entity's confirmed transform.

```jsx
import { useEntityAnimation } from "@webspatial/react-sdk/experimental";
```

> [!CAUTION] > **Boot-gated**
>
> `useEntityAnimation` throws a `WebSpatialRuntimeError` naming `useEntityAnimation` until [`bootSpatial()`](../js-api/bootSpatial.md) has resolved. Mount the animated subtree inside [`<SpatialBoot>`](../react-components/SpatialBoot.md).

## Signature

```ts
useEntityAnimation(config: EntityMotionConfig): UseEntityAnimationResult;
```

```ts
type UseEntityAnimationResult = [
  animation: EntityMotionAnimation,
  api: EntityPlaybackApi,
  entityProps: EntityMotionProps,
];
```

## Config

```ts
{
  // Sparse start/end poses. Units: position in meters, rotation in degrees,
  // scale as unitless multipliers.
  from: { position: { x: 0 }, rotation: { y: 0 } },
  to: { position: { x: 1 }, rotation: { y: 90 } },

  // Or a percentage-keyframe timeline, which takes precedence:
  timeline: {
    from: { position: { x: 0 } },
    "50%": { position: { x: 0.5 }, timingFunction: "easeInOut" },
    to: { position: { x: 1 } },
  },

  duration: 1,          // seconds
  timingFunction: "easeInOut", // linear | easeIn | easeOut | easeInOut
  delay: 0,
  playbackRate: 1,
  loop: false,          // or true, or { reverse: true }
  autoStart: true,

  onStart, onComplete, onStop, onReset, // receive EntityMotionProps (confirmed pose)
  onError, // receives EntityPlaybackError { code, reason }
}
```

## Return

### animation

Pass it to the `<Entity>` `animation` prop:

```jsx
<Entity animation={animation} />
```

### api

Extends the [standard playback controls](./useAnimation.md#api) (`play`, `pause`, `stop`, `reset`, `finish`, and the `isAnimating` / `isPaused` / `finished` / `playState` getters) with:

```ts
set(update: EntityMotionPatch): void; // Commit a sparse transform update;
                                      // confirmed values arrive via entityProps.
```

### entityProps

The last confirmed complete transform from the runtime:

```ts
{
  position?: Vec3; // meters
  rotation?: Vec3; // degrees
  scale?: Vec3;    // unitless multipliers
}
```

## Example

```jsx
import { SpatialBoot } from "@webspatial/react-sdk";
import { Reality, Entity } from "@webspatial/react-sdk";
import { useEntityAnimation } from "@webspatial/react-sdk/experimental";

function SpinningEntity() {
  const [animation, api] = useEntityAnimation({
    from: { rotation: { y: 0 } },
    to: { rotation: { y: 360 } },
    duration: 3,
    loop: true,
  });

  return (
    <SpatialBoot>
      <Reality>
        <Entity animation={animation} />
      </Reality>
    </SpatialBoot>
  );
}
```

## Related APIs

- [`useAnimation`](./useAnimation.md) — timeline motion for spatialized containers.
- [Experimental index](./index.md) — entry contract and shared gating behavior.
- [`<Reality>`](../react-components/Reality.md) — declares entities and their props, including `animation`.

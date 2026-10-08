---
sidebar_position: 3
description: 'Subscribe a React component to spatial runtime readiness.'
---

# `useSpatialReady`

A React hook that returns the current readiness and re-renders the component when the default-entry runtime becomes ready.

## Signature

```ts
import { useSpatialReady } from "@webspatial/react-sdk";

function useSpatialReady(): boolean;
```

## Return value

- Starts as `false` in a WebSpatial runtime and becomes `true` after [`bootSpatial()`](./bootSpatial.md) resolves.
- Returns `false` in plain browsers and during server rendering without subscribing to runtime updates.
- Always returns `true` from `@webspatial/react-sdk/eager`.

The hook classifies the runtime once when the component instance mounts. If the environment is changed by a test harness after mount, remount the component to run detection again.

The hook uses a stable `false` server snapshot. During hydration, the first client render therefore matches server fallback markup even if boot completed before `hydrateRoot()`. The switch to real spatial implementations occurs after hydration commits.

## Example

```tsx
import { useSpatialReady } from "@webspatial/react-sdk";

export function RuntimeStatus() {
  const ready = useSpatialReady();
  return <span>{ready ? "Spatial ready" : "Web fallback"}</span>;
}
```

Calling this hook does not start loading. Use [`<SpatialBoot>`](../../react-components/SpatialBoot.md) or call [`bootSpatial()`](./bootSpatial.md).

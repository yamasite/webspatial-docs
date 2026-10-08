---
sidebar_position: 2
description: 'Read the current spatial runtime readiness synchronously.'
---

# `isSpatialReady`

Returns whether the spatial implementation is ready in the current JavaScript realm.

## Signature

```ts
import { isSpatialReady } from "@webspatial/react-sdk";

function isSpatialReady(): boolean;
```

## Return value

- `true` after the default entry has loaded and stored the spatial implementation successfully.
- `false` before boot, during a load, after a failed load, in a plain browser, and during SSR.
- Always `true` when imported from `@webspatial/react-sdk/eager`.

This is a synchronous snapshot. It does not subscribe a component or start loading. In React components, use [`useSpatialReady`](./useSpatialReady.md) when the UI must re-render as readiness changes.

```tsx
if (isSpatialReady()) {
  startSpatialOnlyWork();
}
```

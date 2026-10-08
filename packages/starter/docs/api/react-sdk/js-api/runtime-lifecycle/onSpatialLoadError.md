---
sidebar_position: 4
description: 'Subscribe to failures while the default entry loads the spatial runtime.'
---

# `onSpatialLoadError`

Registers a listener for spatial implementation load failures.

## Signature

```ts
import { onSpatialLoadError } from "@webspatial/react-sdk";

function onSpatialLoadError(
  listener: (error: WebSpatialBootError) => void,
): () => void;
```

## Behavior

- Every active listener is called once for each failed load attempt, in registration order.
- The listener receives the same [`WebSpatialBootError`](./WebSpatialBootError.md) instance that rejects [`bootSpatial()`](./bootSpatial.md).
- The returned function unsubscribes that listener from later failures.
- A successful retry does not replay earlier errors.
- No listener runs in a plain browser or during SSR because no spatial load is attempted.
- On `@webspatial/react-sdk/eager`, registration is a compatibility no-op and the listener never fires.

A listener that throws does not replace the original boot rejection and does not prevent later listeners from running. Listener exceptions are reported to the console in development.

## Example

```ts
import { onSpatialLoadError } from "@webspatial/react-sdk";

const unsubscribe = onSpatialLoadError((error) => {
  reportRuntimeFailure({
    attempt: error.attempt,
    cause: error.cause,
  });
});

// Stop observing future attempts.
unsubscribe();
```

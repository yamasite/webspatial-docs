<!--
sidebar_position: 4
description: 'Boot the spatial runtime, track readiness, and react to load failures; covers the lazy and eager distribution forms.'
-->

# `bootSpatial` and readiness APIs

## Summary

The default entry loads the spatial implementation lazily. `bootSpatial()` is the single activation path that triggers that load — spatial components and hooks never load it on their own. The companion readiness APIs let you observe when the load finishes, and react when it fails.

```js
import {
  bootSpatial,
  isSpatialReady,
  useSpatialReady,
  onSpatialLoadError,
  WebSpatialBootError,
} from "@webspatial/react-sdk";
```

In React applications, wrapping spatial UI in [`<SpatialBoot>`](../react-components/SpatialBoot.md) already calls `bootSpatial()` for you. The APIs below are for manual control, imperative entry points, and error handling.

## `bootSpatial()`

### Signature

```ts
bootSpatial(): Promise<void>;
```

### Behavior

- **No-op off the runtime.** When no spatial runtime is detectable (a plain browser, or the server), the promise resolves immediately without scheduling any network request.
- **Idempotent.** Concurrent calls share one in-flight promise; after a successful resolution, later calls return an already-resolved promise and schedule no additional work.
- **Retryable.** After a rejection, calling `bootSpatial()` again starts a fresh dynamic import.
- **Failure.** On failure the promise rejects with a [`WebSpatialBootError`](#webspatialbooterror).

### Example: imperative entry

```js
import { bootSpatial } from "@webspatial/react-sdk";
import { createRoot } from "react-dom/client";
import { App } from "./App.jsx";

await bootSpatial();
createRoot(document.getElementById("root")).render(<App />);
```

## `isSpatialReady()`

### Signature

```ts
isSpatialReady(): boolean;
```

Synchronous read. Returns `true` only after the spatial chunk has loaded successfully and the bridge holds the implementation. Returns `false` before boot, on failure, and in non-WebSpatial environments.

## `useSpatialReady()`

### Signature

```ts
useSpatialReady(): boolean;
```

A React hook subscribed to bridge readiness: the consuming component re-renders when the spatial chunk becomes ready.

- Returns `false` during SSR.
- In non-WebSpatial browsers it stays `false` without per-render subscription bookkeeping.

```jsx
import { useSpatialReady } from "@webspatial/react-sdk";

function StatusBadge() {
  const ready = useSpatialReady();
  return ready ? <span>Spatial ready</span> : <span>Loading…</span>;
}
```

## `onSpatialLoadError()`

### Signature

```ts
onSpatialLoadError(listener: (error: WebSpatialBootError) => void): () => void;
```

Registers a callback invoked on every failed load attempt. Returns an unsubscribe function.

- Listeners are called in registration order, once per failed attempt.
- A successful retry after a failure does **not** replay earlier errors to listeners.
- Errors thrown inside a listener are isolated: they are logged in development and never corrupt the boot promise or replace the rejection reason.

```js
import { onSpatialLoadError, bootSpatial } from "@webspatial/react-sdk";

const unsubscribe = onSpatialLoadError((err) => {
  console.error(`Boot failed on attempt ${err.attempt}:`, err.cause);
});

await bootSpatial().catch(() => {
  // Show a retry button; bootSpatial() can be called again.
});
```

## `WebSpatialBootError`

The rejection value of `bootSpatial()` and the argument to `onSpatialLoadError` callbacks.

```ts
class WebSpatialBootError extends Error {
  readonly name: "WebSpatialBootError";
  readonly cause: unknown; // The original dynamic-import failure.
  readonly attempt: number; // 1-based retry number.
}
```

## Distribution forms: lazy vs. eager

The package ships two import roots:

|                            | `@webspatial/react-sdk` (default)                      | `@webspatial/react-sdk/eager`             |
| -------------------------- | ------------------------------------------------------ | ----------------------------------------- |
| Audience                   | Web-first apps that progressively enhance into spatial | Spatial-only apps (fixed spatial devices) |
| Spatial chunk              | Dynamic-imported on `bootSpatial()`                    | Statically linked into the bundle         |
| Network requests           | Two (page + spatial chunk)                             | One                                       |
| `bootSpatial()` round-trip | Required                                               | None                                      |

On the eager entry, the four APIs above exist as compatibility stubs so that migrating is **import-root-only** — you change the import path, not the symbols:

- `bootSpatial()` resolves immediately (and logs a one-shot development warning that boot is unnecessary).
- `isSpatialReady()` and `useSpatialReady()` always return `true`.
- `onSpatialLoadError()` registers the callback but never invokes it — the eager entry has no dynamic load, and therefore no load failure.

> [!CAUTION] > **Do not mix import roots**
>
> Use exactly one import root per application bundle, including transitive dependencies. If both `@webspatial/react-sdk` and `@webspatial/react-sdk/eager` load in the same JavaScript realm, the second entry throws `WebSpatialMixedEntryError` at module evaluation. The error exposes `previous` and `next` (`"lazy"` or `"eager"`) and names the two import paths in its message.

## Related APIs

- [`<SpatialBoot>`](../react-components/SpatialBoot.md) — the React wrapper that invokes `bootSpatial()` for you.
- [`WebSpatialRuntime`](WebSpatialRuntime.md) — probe runtime capabilities once booted.
- [`userAgent`](../dom-api/userAgent.md) — lower-level runtime detection.

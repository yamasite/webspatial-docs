---
sidebar_position: 1
description: 'Load the spatial implementation on demand in a WebSpatial runtime.'
---

# `bootSpatial`

Loads the spatial implementation when the app runs in a WebSpatial runtime. Use [`<SpatialBoot>`](../../react-components/SpatialBoot.md) for the usual React integration; call `bootSpatial()` directly when you need imperative control.

## Signature

```ts
import { bootSpatial } from "@webspatial/react-sdk";

function bootSpatial(): Promise<void>;
```

## Behavior

| Environment                   | Result                                                                                                                                             |
| ----------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| WebSpatial runtime            | Dynamically loads the spatial implementation and resolves only after it is ready.                                                                  |
| Plain browser                 | Resolves without loading the spatial chunk. Web fallbacks remain active.                                                                           |
| Server-side rendering         | Resolves without accessing `window` or scheduling a dynamic import.                                                                                |
| `@webspatial/react-sdk/eager` | Resolves immediately because the spatial implementation is already linked. In development, the first call logs a warning that boot is unnecessary. |

Concurrent calls during one load attempt share the same underlying load. After a successful load, later calls resolve from the cached result and do not schedule another request.

If loading fails, the promise rejects with [`WebSpatialBootError`](./WebSpatialBootError.md). A later call starts a fresh attempt. Only one retry can be in flight at a time.

## Example: boot before a CSR render

```tsx
import { bootSpatial } from "@webspatial/react-sdk";
import { createRoot } from "react-dom/client";
import { App } from "./App";

await bootSpatial();
createRoot(document.getElementById("root")!).render(<App />);
```

For SSR and hydration, use the default entry. Booting before or after `hydrateRoot()` is supported: the hydration pass still uses fallback markup, and real spatial implementations mount on a later React commit. Do not server-render spatial primitives imported from `@webspatial/react-sdk/eager`.

:::caution[Use one entry root]
Do not mix `@webspatial/react-sdk` and `@webspatial/react-sdk/eager` in one application bundle. See [`WebSpatialMixedEntryError`](./WebSpatialMixedEntryError.md).
:::

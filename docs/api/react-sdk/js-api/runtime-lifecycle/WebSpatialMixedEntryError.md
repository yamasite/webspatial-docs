---
sidebar_position: 6
description: 'Diagnose a bundle that loads both lazy and eager React SDK entry roots.'
---

# `WebSpatialMixedEntryError`

Thrown when one JavaScript realm loads both `@webspatial/react-sdk` and `@webspatial/react-sdk/eager`.

## Shape

```ts
import { WebSpatialMixedEntryError } from "@webspatial/react-sdk";

type ReactSdkEntryKind = "lazy" | "eager";

class WebSpatialMixedEntryError extends Error {
  readonly previous: ReactSdkEntryKind;
  readonly next: ReactSdkEntryKind;
}
```

| Property   | Meaning                                  |
| ---------- | ---------------------------------------- |
| `previous` | The entry root registered first.         |
| `next`     | The conflicting entry root loaded later. |

Choose exactly one entry root for the whole application bundle, including transitive dependencies:

- Use `@webspatial/react-sdk` for web-first apps, SSR, hydration, and progressive enhancement.
- Use `@webspatial/react-sdk/eager` only for client-rendered, spatial-only apps that want the real implementation linked immediately.

```ts
// Supported: one root throughout the bundle.
import { Model, bootSpatial } from "@webspatial/react-sdk";

// Unsupported in the same bundle:
// import { Reality } from "@webspatial/react-sdk/eager";
```

Repeated registration of the same root is safe. After the first mixed-entry error, the registry reports the conflict only once for the page lifetime.

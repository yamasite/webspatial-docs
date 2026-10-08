---
sidebar_position: 5
description: 'Inspect a failed spatial runtime load and its retry attempt.'
---

# `WebSpatialBootError`

The error class used when the default entry cannot load the spatial implementation.

## Shape

```ts
import { WebSpatialBootError } from "@webspatial/react-sdk";

class WebSpatialBootError extends Error {
  readonly cause: unknown;
  readonly attempt: number;
}
```

| Property  | Meaning                                                                       |
| --------- | ----------------------------------------------------------------------------- |
| `name`    | Always `"WebSpatialBootError"`.                                               |
| `message` | `Failed to boot WebSpatial runtime on attempt N`.                             |
| `cause`   | The original value rejected by the dynamic import.                            |
| `attempt` | The 1-based load attempt number. It increments when a failed boot is retried. |

`bootSpatial()` never exposes a raw dynamic-import failure: it rejects with this wrapper. The same instance is sent to all active [`onSpatialLoadError`](./onSpatialLoadError.md) listeners for that attempt.

```ts
import {
  bootSpatial,
  WebSpatialBootError,
} from "@webspatial/react-sdk";

try {
  await bootSpatial();
} catch (error) {
  if (error instanceof WebSpatialBootError) {
    console.error(`Boot attempt ${error.attempt} failed`, error.cause);
  }
}
```

The class is also exported by `@webspatial/react-sdk/eager` for source compatibility, but eager mode does not dynamically load the runtime and therefore does not throw it during boot.

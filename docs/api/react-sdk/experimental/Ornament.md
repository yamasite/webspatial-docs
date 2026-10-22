---
sidebar_position: 2
description: 'Float 2D content as an ornament anchored in space, from the experimental entry.'
---

# `<Ornament>`

## Summary

`Ornament` renders your 2D children as a floating surface anchored in space, separate from the page's spatialized containers. It is published from the experimental entry:

```jsx
import { Ornament } from "@webspatial/react-sdk/experimental";
```

Your children render inside the ornament's own child window, so normal HTML, CSS, and layout apply inside it.

## Props

| Prop               | Type                                                                        | Description                                                                                                  |
| ------------------ | --------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------ |
| `attachmentAnchor` | `OrnamentPoint3D`                                                           | Optional. Where the ornament attaches.                                                                       |
| `contentAlignment` | `OrnamentPoint3D`                                                           | Optional. How content aligns within the ornament.                                                            |
| `visibility`       | `OrnamentVisibility`                                                        | Optional.                                                                                                    |
| `width`            | `number`                                                                    | Optional.                                                                                                    |
| `height`           | `number`                                                                    | Optional.                                                                                                    |
| `cornerRadius`     | `Partial<CornerRadius>`                                                     | Optional. Per-corner radius overrides.                                                                       |
| `children`         | `ReactNode`                                                                 | Required. The 2D content to float.                                                                           |
| `style`            | `CSSProperties` & `{ "--xr-background-material"?: BackgroundMaterialType }` | Optional. Styles applied into the ornament's child window; the CSS variable selects the background material. |

## Behavior

- **Capability-gated.** If `WebSpatialRuntime.supports("Ornament")` is `false`, the component renders `null`.
- **Boot-gated.** If the spatial runtime has not finished booting, the component renders a development diagnostic reminding you to wrap the subtree in [`<SpatialBoot>`](../react-components/SpatialBoot.md).
- **No nesting.** An `Ornament` cannot be used inside another ornament's content or inside an attachment's content; such usage logs a development warning and renders nothing.
- Prop changes while mounted are applied to the live ornament without recreating it.

## Example

```jsx
import { Ornament } from "@webspatial/react-sdk/experimental";
import { SpatialBoot } from "@webspatial/react-sdk";

export function HelpCard() {
  return (
    <SpatialBoot>
      <Ornament width={400} height={200} style={{ "--xr-background-material": "translucent" }}>
        <h2>Need help?</h2>
        <p>This card floats in space, outside the main page.</p>
      </Ornament>
    </SpatialBoot>
  );
}
```

## Related APIs

- [Experimental index](./index.md) — entry contract and shared gating behavior.
- [`WebSpatialRuntime.supports`](../js-api/WebSpatialRuntime.md) — the probe behind the `Ornament` gate.
- [Debug the spatial scene](../../../how-to/debug.md) — inspecting ornaments and other scene objects at runtime.

---
sidebar_position: 3
description: 'Give scene and element backplates a transparent or real-time translucent material background.'
---

# `background-material`

## Summary

`background-material` is a new CSS property added by WebSpatial on top of existing Web standards. It allows both [webpage windows floating in space](../../../concepts/spatial-scenes.md) and [spatialized HTML elements](../../../concepts/spatialized-html-elements.md) to be fully transparent or to use a real-time rendered translucent material as their background, instead of being limited to fixed opaque colors.

## Applies To

The current WebSpatial SDK only supports `background-material` on the HTML element that represents the webpage window itself, and on 2D HTML elements [marked as spatialized HTML elements](../react-components/jsx-marker.md), excluding [3D container elements](../../../concepts/3d-content-containers.md).

## Mental Model

The property behaves differently on the two targets:

- On the webpage window, `none` keeps the native opaque backplate; `transparent` removes it entirely so inner elements appear to float; a translucent value replaces it with a material rendered live from the viewpoint and environment.
- On spatialized HTML elements, `none` and `transparent` both mean "no backplate"; a translucent value adds one.

## Syntax

:::caution[Temporary prefixed CSS name]
Before standardization is complete, CSS properties in the WebSpatial API need the `-xr-` prefix.
In the current implementation of WebSpatial SDK, for performance reasons, new CSS APIs are implemented through CSS custom properties, so the property name for `background-material` must be written as `--xr-background-material` both in CSS styles and in the `style` attribute.
:::

Example for the webpage window:

```css
html {
  --xr-background-material: transparent;
}
```

Example for a spatialized HTML element:

```css
div {
  position: absolute;
  --xr-back: 100px;
  --xr-background-material: translucent;
}
```

## Value Grammar

`background-material` takes an enum value:

- `none` — the default. The webpage window keeps its native opaque background; spatialized HTML elements get a transparent background.
- `translucent` — a glass-like translucent material.
- `thick` — a thicker translucent material.
- `regular` — a regular (medium) translucent material.
- `thin` — a thin translucent material.
- `transparent` — a fully transparent background with no backplate.

## Initial Value

The initial value is effectively:

```css
--xr-background-material: none;
```

## Inherited

Child elements do not inherit the `background-material` property of their parent element.

## Fallback Behavior

In environments that do not support WebSpatial, `background-material` is ignored automatically.

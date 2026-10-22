---
sidebar_position: 1
description: 'Configure the Start Scene from the Web App Manifest before any page code runs.'
---

# `xr_main_scene`

In the [Web App Manifest](../../../concepts/webspatial-app.md#web-app) of a [WebSpatial App](../../../concepts/webspatial-app.md), this new field can be used to configure the [initialization properties](../../../concepts/spatial-scenes.md#scene-initialization) of the [Start Scene](../../../concepts/spatial-scenes.md#start-scene).

:::caution[Temporary prefixed manifest key]
Because this is a new property added by WebSpatial API to the Web App Manifest standard, it needs the `xr_` prefix before standardization is complete, so `main_scene` must be written as `xr_main_scene`.
:::

The following initialization properties are supported:

- [`type`](../scene-options/type.md)
- [`default_size`](../scene-options/defaultSize.md)
- [`resizability`](../scene-options/resizability.md)
- [`worldScaling`](../scene-options/worldScaling.md)
- [`worldAlignment`](../scene-options/worldAlignment.md)
- [`baseplateVisibility`](../scene-options/baseplateVisibility.md)

:::note[Build-time keys use camelCase]
`xr_main_scene` is the build-time key: it is read by WebSpatial Builder when packaging, and it follows the published JSON schema. Inside it, the scene options are spelled in camelCase — `worldScaling`, `worldAlignment`, `baseplateVisibility`, and the `resizability` sub-keys `minWidth` / `maxWidth` / `minHeight` / `maxHeight`. Only `default_size` stays snake_case, matching the published schema. Writing the option names as `world_scaling`, `world_alignment`, `baseplate_visibility`, or `min_width` inside `xr_main_scene` is ignored by Builder.

At runtime, the in-browser polyfill reads a [different, separate key](#runtime-key-xr_spatial_scene) (`xr_spatial_scene`) that accepts both spellings.
:::

## Schema

```ts
{
  type?: "volume" | "window"
  default_size?: {
    width?: number | string
    height?: number | string
    depth?: number | string
  }
  resizability?: {
    minWidth?: number | string
    maxWidth?: number | string
    minHeight?: number | string
    maxHeight?: number | string
  }
  worldScaling?: "automatic" | "dynamic"
  worldAlignment?: "adaptive" | "automatic" | "gravityAligned"
  baseplateVisibility?: "automatic" | "visible" | "hidden"
}
```

Example:

```json
{
  "name": "example app",
  "start_url": "/",
  "xr_main_scene": {
    "type": "volume",
    "default_size": {
      "width": 100,
      "height": 100,
      "depth": "1m"
    },
    "resizability": {
      "minWidth": 50,
      "maxWidth": 500,
      "minHeight": 50,
      "maxHeight": 500
    },
    "worldScaling": "dynamic",
    "worldAlignment": "gravityAligned",
    "baseplateVisibility": "hidden"
  }
}
```

## Runtime key: `xr_spatial_scene` {#runtime-key-xr_spatial_scene}

`xr_main_scene` above is the build-time key consumed by WebSpatial Builder during packaging. Independently, the WebSpatial runtime polyfill reads a separate manifest key, `xr_spatial_scene`, to apply Start Scene defaults in the browser. The two keys are not interchangeable: Builder ignores `xr_spatial_scene`, and the runtime polyfill ignores `xr_main_scene`.

`xr_spatial_scene` accepts the same scene options, but within a single object it accepts both camelCase and snake_case spellings; when both are present on the same object, the snake_case value wins. It also supports per-scene-type `overrides`:

```ts
{
  xr_spatial_scene?: {
    // top-level defaults; each option accepts camelCase or snake_case
    default_size?: { width?: number | string; height?: number | string; depth?: number | string }
    resizability?: {
      min_width?: number | string   // or minWidth
      max_width?: number | string  // or maxWidth
      min_height?: number | string // or minHeight
      max_height?: number | string // or maxHeight
    }
    world_scaling?: "automatic" | "dynamic"                 // or worldScaling
    world_alignment?: "adaptive" | "automatic" | "gravityAligned" // or worldAlignment
    baseplate_visibility?: "automatic" | "visible" | "hidden"    // or baseplateVisibility
    overrides?: {
      window_scene?: { /* defaults applied to window scenes */ } // or windowScene
      volume_scene?: { /* defaults applied to volume scenes */ } // or volumeScene
    }
  }
}
```

The `overrides.window_scene` and `overrides.volume_scene` objects are deep-merged on top of the top-level defaults and take precedence for that scene type. Snake_case wins only within the same object; it does not override values on another layer.

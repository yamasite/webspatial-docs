---
sidebar_position: 1
description: '在网页代码运行前，通过 Web App Manifest 配置起始场景的初始化属性。'
---

# `xr_main_scene`

在 [WebSpatial App](../../../concepts/webspatial-app.md) 的 [Web App Manifest](../../../concepts/webspatial-app.md#web-app) 中，可以用这个新字段设置[起始场景](../../../concepts/spatial-scenes.md#start-scene)的[初始化属性](../../../concepts/spatial-scenes.md#scene-initialization)。

:::caution[标准化完成前的临时 Manifest 键名]
WebSpatial API 在 Web App Manifest 标准中扩展出的新属性，在标准化完成前，使用时需要加上 `xr_` 前缀，因此 `main_scene` 的属性名要写成 `xr_main_scene`。
:::

支持以下初始化属性：

- [`type`](../scene-options/type.md)
- [`default_size`](../scene-options/defaultSize.md)
- [`resizability`](../scene-options/resizability.md)
- [`worldScaling`](../scene-options/worldScaling.md)
- [`worldAlignment`](../scene-options/worldAlignment.md)
- [`baseplateVisibility`](../scene-options/baseplateVisibility.md)

:::note[构建期键名使用 camelCase]
`xr_main_scene` 是构建期键：它由 WebSpatial Builder 在打包时读取，并遵循已发布的 JSON Schema。其中的场景属性使用 camelCase 写法——`worldScaling`、`worldAlignment`、`baseplateVisibility`，以及 `resizability` 的子字段 `minWidth` / `maxWidth` / `minHeight` / `maxHeight`。只有 `default_size` 保持 snake_case，与已发布的 Schema 一致。在 `xr_main_scene` 里把属性写成 `world_scaling`、`world_alignment`、`baseplate_visibility` 或 `min_width`，会被 Builder 忽略。

运行时，浏览器内的 polyfill 读取的是[另一个独立键 `xr_spatial_scene`](#runtime-key-xr_spatial_scene)，它同时接受两种写法。
:::

## 结构定义 {#schema}

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

示例：

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

## 运行时键：`xr_spatial_scene` {#runtime-key-xr_spatial_scene}

上面的 `xr_main_scene` 是 WebSpatial Builder 在打包时读取的构建期键。除此之外，WebSpatial 运行时 polyfill 还会读取另一个独立的 Manifest 键 `xr_spatial_scene`，用于在浏览器中应用起始场景的默认值。这两个键不能互换：Builder 会忽略 `xr_spatial_scene`，运行时 polyfill 会忽略 `xr_main_scene`。

`xr_spatial_scene` 接受相同的场景属性，但在同一个对象内同时接受 camelCase 和 snake_case 两种写法；当同一个对象里两种写法同时存在时，以 snake_case 为准。它还支持按场景类型设置 `overrides`：

```ts
{
  xr_spatial_scene?: {
    // 顶层默认值；每个属性都接受 camelCase 或 snake_case
    default_size?: { width?: number | string; height?: number | string; depth?: number | string }
    resizability?: {
      min_width?: number | string   // 或 minWidth
      max_width?: number | string  // 或 maxWidth
      min_height?: number | string // 或 minHeight
      max_height?: number | string // 或 maxHeight
    }
    world_scaling?: "automatic" | "dynamic"                 // 或 worldScaling
    world_alignment?: "adaptive" | "automatic" | "gravityAligned" // 或 worldAlignment
    baseplate_visibility?: "automatic" | "visible" | "hidden"    // 或 baseplateVisibility
    overrides?: {
      window_scene?: { /* 应用于 window 场景的默认值 */ } // 或 windowScene
      volume_scene?: { /* 应用于 volume 场景的默认值 */ } // 或 volumeScene
    }
  }
}
```

`overrides.window_scene` 与 `overrides.volume_scene` 会在顶层默认值之上做深度合并，并对对应场景类型优先生效。snake_case 只在同一对象内部胜出，不会覆盖另一层上的值。

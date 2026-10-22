---
sidebar_position: 6
description: 'Show or hide the baseplate under a volume Spatial Scene.'
---

# `baseplateVisibility`

One of the [initialization properties](../../../concepts/spatial-scenes.md#scene-initialization) of a [Spatial Scene](../../../concepts/spatial-scenes.md). Only Spatial Scenes of type `volume` support `baseplateVisibility`, and it affects the visibility of the baseplate under the Volume.

## Availability {#declared-in}

| Item          | Value                                                                  |
| ------------- | ---------------------------------------------------------------------- |
| Applies to    | Only `volume` Spatial Scenes.                                          |
| New scenes    | Set through [`initScene`](../js-api/initScene.md).                     |
| Start scene   | Set through the [Web App Manifest](../manifest-options/main-scene.md). |
| Default value | `"automatic"`                                                          |

## Type Signature {#type}

`"automatic" | "visible" | "hidden"`

## Examples

### Using `initScene`

```js title="Set baseplateVisibility for a new scene" {6-7}
import { initScene } from "@webspatial/react-sdk";

initScene("newSceneName", defaultConfig => {
  return {
    ...defaultConfig,
    type: "volume",
    baseplateVisibility: "hidden",
  };
});
window.open(newSceneUrl, "newSceneName");
```

### Using the Web App Manifest

:::caution[Manifest key naming]
In the build-time `xr_main_scene` key, write the option in camelCase — `baseplateVisibility` — exactly as shown below. WebSpatial Builder ignores the snake_case spelling `baseplate_visibility` inside `xr_main_scene`. The separate runtime `xr_spatial_scene` key accepts both spellings; see [main-scene](../manifest-options/main-scene.md#runtime-key-xr_spatial_scene).
:::

```json title="Set baseplateVisibility for the start scene" {5-6}
{
  "name": "example app",
  "start_url": "/",
  "xr_main_scene": {
    "type": "volume",
    "baseplateVisibility": "hidden"
  }
}
```

## Default Value {#default-value}

`"automatic"`

## Accepted Values {#accepted-values}

| Value         | Meaning                                         |
| ------------- | ----------------------------------------------- |
| `"automatic"` | The baseplate is shown when needed.             |
| `"visible"`   | The baseplate is always shown under the volume. |
| `"hidden"`    | The baseplate is always hidden.                 |

<!--
sidebar_position: 4
description: 'Control whether a Spatial Scene keeps a constant apparent size or scales with distance.'
-->

# `worldScaling`

One of the [initialization properties](../../../concepts/spatial-scenes.md#scene-initialization) of a [Spatial Scene](../../../concepts/spatial-scenes.md); it controls how a scene responds when the user drags it horizontally after creation.

## Availability

| Item          | Value                                                                  |
| ------------- | ---------------------------------------------------------------------- |
| Applies to    | Only `volume` Spatial Scenes.                                          |
| New scenes    | Set through [`initScene`](../js-api/initScene.md).                     |
| Start scene   | Set through the [Web App Manifest](../manifest-options/main-scene.md). |
| Default value | `"automatic"`                                                          |

## Type Signature

`"automatic" | "dynamic"`

## Examples

### Using `initScene`

**Set worldScaling for a new scene**

```js
import { initScene } from "@webspatial/react-sdk";

initScene("newSceneName", defaultConfig => {
  return {
    ...defaultConfig,
    type: "volume",
    worldScaling: "dynamic",
  };
});
window.open(newSceneUrl, "newSceneName");
```

### Using the Web App Manifest

> [!CAUTION] > **Manifest key naming**
>
> In the build-time `xr_main_scene` key, write the option in camelCase — `worldScaling` — exactly as shown below. WebSpatial Builder ignores the snake_case spelling `world_scaling` inside `xr_main_scene`. The separate runtime `xr_spatial_scene` key accepts both spellings; see [main-scene](../manifest-options/main-scene.md#runtime-key-xr_spatial_scene).

**Set worldScaling for the start scene**

```json
{
  "name": "example app",
  "start_url": "/",
  "xr_main_scene": {
    "type": "volume",
    "worldScaling": "dynamic"
  }
}
```

## Default Value

`"automatic"`

## Accepted Values

| Value         | Meaning                                                                                                                                 |
| ------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| `"automatic"` | Shows a distance-based scaling effect during movement, like a real-world object.                                                        |
| `"dynamic"`   | Keeps a constant size relative to the user's field of view during movement, just like the default behavior of a `window` Spatial Scene. |

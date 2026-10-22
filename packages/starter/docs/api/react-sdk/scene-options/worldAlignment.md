<!--
sidebar_position: 5
description: 'Control whether a Spatial Scene stays upright, faces the user, or behaves more like a real object.'
-->

# `worldAlignment`

One of the [initialization properties](../../../concepts/spatial-scenes.md#scene-initialization) of a [Spatial Scene](../../../concepts/spatial-scenes.md); it controls how a scene responds when the user drags it vertically after creation.

## Availability

| Item          | Value                                                                  |
| ------------- | ---------------------------------------------------------------------- |
| Applies to    | Only `volume` Spatial Scenes.                                          |
| New scenes    | Set through [`initScene`](../js-api/initScene.md).                     |
| Start scene   | Set through the [Web App Manifest](../manifest-options/main-scene.md). |
| Default value | `"automatic"`                                                          |

## Type Signature

`"adaptive" | "automatic" | "gravityAligned"`

## Examples

### Using `initScene`

**Set worldAlignment for a new scene**

```js
import { initScene } from "@webspatial/react-sdk";

initScene("newSceneName", defaultConfig => {
  return {
    ...defaultConfig,
    type: "volume",
    worldAlignment: "gravityAligned",
  };
});
window.open(newSceneUrl, "newSceneName");
```

### Using the Web App Manifest

> [!CAUTION] > **Manifest key naming**
>
> In the build-time `xr_main_scene` key, write the option in camelCase — `worldAlignment` — exactly as shown below. WebSpatial Builder ignores the snake_case spelling `world_alignment` inside `xr_main_scene`. The separate runtime `xr_spatial_scene` key accepts both spellings; see [main-scene](../manifest-options/main-scene.md#runtime-key-xr_spatial_scene).

**Set worldAlignment for the start scene**

```json
{
  "name": "example app",
  "start_url": "/",
  "xr_main_scene": {
    "type": "volume",
    "worldAlignment": "gravityAligned"
  }
}
```

## Default Value

`"automatic"`

## Accepted Values

| Value              | Meaning                                                                                                              |
| ------------------ | -------------------------------------------------------------------------------------------------------------------- |
| `"adaptive"`       | The runtime adaptively chooses how the scene aligns based on context.                                                |
| `"automatic"`      | Tilts automatically during vertical movement so it always faces the user.                                            |
| `"gravityAligned"` | Does not tilt automatically during vertical movement and always stays aligned with gravity like a real-world object. |

---
sidebar_position: 3
description: 'Limit how far users can resize a Spatial Scene after it opens.'
---

# `resizability`

One of the [initialization properties](../../../concepts/spatial-scenes.md#scene-initialization) of a [Spatial Scene](../../../concepts/spatial-scenes.md), `resizability` affects the constraints applied when a user drags the Spatial Scene to resize it after creation. It can provide separate min and max constraints for the width and height of the Spatial Scene.

## Availability {#declared-in}

| Item          | Value                                                                  |
| ------------- | ---------------------------------------------------------------------- |
| Applies to    | All resizable Spatial Scenes.                                          |
| New scenes    | Set through [`initScene`](../js-api/initScene.md).                     |
| Start scene   | Set through the [Web App Manifest](../manifest-options/main-scene.md). |
| Default value | None.                                                                  |

## Type Signature {#type}

```ts title="resizability type"
{
  minWidth?: number | string
  minHeight?: number | string
  maxWidth?: number | string
  maxHeight?: number | string
}
```

## Examples

### Using `initScene`

```js title="Set resizability for a new scene" {6-12}
import { initScene } from "@webspatial/react-sdk";

initScene("newSceneName", defaultConfig => {
  return {
    ...defaultConfig,
    type: "volume",
    resizability: {
      minWidth: 100,
      minHeight: "100px",
      maxWidth: 200,
      maxHeight: 200,
    },
  };
});
window.open(newSceneUrl, "newSceneName");
```

### Using the Web App Manifest

:::caution[Manifest key naming]
In the build-time `xr_main_scene` key, write the sub-fields in camelCase — `minWidth`, `minHeight`, `maxWidth`, `maxHeight` — exactly as shown below. WebSpatial Builder ignores the snake_case spellings (`min_width`, and so on) inside `xr_main_scene`. The separate runtime `xr_spatial_scene` key accepts both spellings; see [main-scene](../manifest-options/main-scene.md#runtime-key-xr_spatial_scene).
:::

```json title="Set resizability for the start scene" {5-10}
{
  "name": "example app",
  "start_url": "/",
  "xr_main_scene": {
    "resizability": {
      "minWidth": 100,
      "minHeight": "100px",
      "maxWidth": 200,
      "maxHeight": 200
    }
  }
}
```

## Default Value {#default-value}

None.

## Accepted Values {#accepted-values}

| Field       | Accepted value                                                                                      |
| ----------- | --------------------------------------------------------------------------------------------------- |
| `minWidth`  | A number with `px` as the default unit, or a string with a unit suffix such as `"100px"` or `"2m"`. |
| `minHeight` | A number with `px` as the default unit, or a string with a unit suffix such as `"100px"` or `"2m"`. |
| `maxWidth`  | A number with `px` as the default unit, or a string with a unit suffix such as `"100px"` or `"2m"`. |
| `maxHeight` | A number with `px` as the default unit, or a string with a unit suffix such as `"100px"` or `"2m"`. |

---
sidebar_position: 4
description: '控制空间场景是保持视觉大小恒定，还是随距离产生缩放效果。'
---

# `worldScaling`

[空间场景](../../../concepts/spatial-scenes.md)的[初始化属性](../../../concepts/spatial-scenes.md#scene-initialization)之一，`worldScaling` 会影响空间场景创建后被用户拖拽在水平方向上移动时的行为。
只有 `volume` 类型的空间场景才支持 `worldScaling`。

## 适用范围与设置方式 {#declared-in}

| 项目     | 内容                                                              |
| -------- | ----------------------------------------------------------------- |
| 适用于   | 仅适用于 `volume` 类型空间场景。                                  |
| 新场景   | 通过 [`initScene`](../js-api/initScene.md) 设置。                 |
| 起始场景 | 通过 [Web App Manifest](../manifest-options/main-scene.md) 设置。 |
| 默认值   | `"automatic"`                                                     |

## 类型定义 {#type}

`"automatic" | "dynamic"`

## 示例

### 使用 `initScene`

```js title="通过 initScene 设置新场景的 worldScaling" {6-7}
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

### 使用 Web App Manifest

:::caution[Manifest 属性命名]
在构建期键 `xr_main_scene` 里，请按下面示例用 camelCase 书写属性名 `worldScaling`。WebSpatial Builder 会忽略 `xr_main_scene` 内的下划线写法 `world_scaling`。运行时键 `xr_spatial_scene` 同时接受两种写法，详见 [main-scene](../manifest-options/main-scene.md#runtime-key-xr_spatial_scene)。
:::

```json title="通过 Web App Manifest 设置起始场景的 worldScaling" {5-6}
{
  "name": "example app",
  "start_url": "/",
  "xr_main_scene": {
    "type": "volume",
    "worldScaling": "dynamic"
  }
}
```

## 默认值 {#default-value}

`"automatic"`

## 可接受的值 {#accepted-values}

| 取值          | 含义                                                                           |
| ------------- | ------------------------------------------------------------------------------ |
| `"automatic"` | 移动时会产生近大远小的缩放效果，更像现实世界中的物体。                         |
| `"dynamic"`   | 移动时始终相对于用户视野保持大小不变，和 `window` 类型空间场景的默认行为一致。 |

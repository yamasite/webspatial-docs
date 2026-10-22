---
sidebar_position: 5
description: '控制空间场景是保持竖直、始终朝向用户，还是更像真实物体。'
---

# `worldAlignment`

[空间场景](../../../concepts/spatial-scenes.md)的[初始化属性](../../../concepts/spatial-scenes.md#scene-initialization)之一，`worldAlignment` 会影响空间场景创建后被用户拖拽在垂直方向上移动时的行为。
只有 `volume` 类型的空间场景才支持 `worldAlignment`。

## 适用范围与设置方式 {#declared-in}

| 项目     | 内容                                                              |
| -------- | ----------------------------------------------------------------- |
| 适用于   | 仅适用于 `volume` 类型空间场景。                                  |
| 新场景   | 通过 [`initScene`](../js-api/initScene.md) 设置。                 |
| 起始场景 | 通过 [Web App Manifest](../manifest-options/main-scene.md) 设置。 |
| 默认值   | `"automatic"`                                                     |

## 类型定义 {#type}

`"adaptive" | "automatic" | "gravityAligned"`

## 示例

### 使用 `initScene`

```js title="通过 initScene 设置新场景的 worldAlignment" {6-7}
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

### 使用 Web App Manifest

:::caution[Manifest 属性命名]
在构建期键 `xr_main_scene` 里，请按下面示例用 camelCase 书写属性名 `worldAlignment`。WebSpatial Builder 会忽略 `xr_main_scene` 内的下划线写法 `world_alignment`。运行时键 `xr_spatial_scene` 同时接受两种写法，详见 [main-scene](../manifest-options/main-scene.md#runtime-key-xr_spatial_scene)。
:::

```json title="通过 Web App Manifest 设置起始场景的 worldAlignment" {5-6}
{
  "name": "example app",
  "start_url": "/",
  "xr_main_scene": {
    "type": "volume",
    "worldAlignment": "gravityAligned"
  }
}
```

## 默认值 {#default-value}

`"automatic"`

## 可接受的值 {#accepted-values}

| 取值               | 含义                                                               |
| ------------------ | ------------------------------------------------------------------ |
| `"adaptive"`       | 运行时会根据上下文自适应选择场景的对齐方式。                       |
| `"automatic"`      | 上下移动时会自动倾斜，始终朝向用户。                               |
| `"gravityAligned"` | 上下移动时不会自动倾斜，始终和重力方向对齐，更像现实世界中的物体。 |

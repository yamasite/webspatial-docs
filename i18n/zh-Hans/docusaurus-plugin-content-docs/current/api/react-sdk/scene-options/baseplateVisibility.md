---
sidebar_position: 6
description: '控制 volume 类型空间场景底板是否可见。'
---

# `baseplateVisibility`

[空间场景](../../../concepts/spatial-scenes.md)的[初始化属性](../../../concepts/spatial-scenes.md#scene-initialization)之一。只有 `volume` 类型的空间场景才支持设置 `baseplateVisibility`，它会影响 Volume 下方底板的可见性。

## 适用范围与设置方式 {#declared-in}

| 项目     | 内容                                                              |
| -------- | ----------------------------------------------------------------- |
| 适用于   | 仅适用于 `volume` 类型空间场景。                                  |
| 新场景   | 通过 [`initScene`](../js-api/initScene.md) 设置。                 |
| 起始场景 | 通过 [Web App Manifest](../manifest-options/main-scene.md) 设置。 |
| 默认值   | `"automatic"`                                                     |

## 类型定义 {#type}

`"automatic" | "visible" | "hidden"`

## 示例

### 使用 `initScene`

```js title="通过 initScene 设置新场景的 baseplateVisibility" {6-7}
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

### 使用 Web App Manifest

:::caution[Manifest 属性命名]
在构建期键 `xr_main_scene` 里，请按下面示例用 camelCase 书写属性名 `baseplateVisibility`。WebSpatial Builder 会忽略 `xr_main_scene` 内的下划线写法 `baseplate_visibility`。运行时键 `xr_spatial_scene` 同时接受两种写法，详见 [main-scene](../manifest-options/main-scene.md#runtime-key-xr_spatial_scene)。
:::

```json title="通过 Web App Manifest 设置起始场景的 baseplateVisibility" {5-6}
{
  "name": "example app",
  "start_url": "/",
  "xr_main_scene": {
    "type": "volume",
    "baseplateVisibility": "hidden"
  }
}
```

## 默认值 {#default-value}

`"automatic"`

## 可接受的值 {#accepted-values}

| 取值          | 含义                         |
| ------------- | ---------------------------- |
| `"automatic"` | 底板按需显示。               |
| `"visible"`   | 底板始终显示在 Volume 下方。 |
| `"hidden"`    | 底板始终隐藏。               |

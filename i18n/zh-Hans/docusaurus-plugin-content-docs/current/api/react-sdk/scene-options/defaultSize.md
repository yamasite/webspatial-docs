---
sidebar_position: 2
description: '设置空间场景创建时请求的初始宽度、高度和深度。'
---

# `defaultSize`

[空间场景](../../../concepts/spatial-scenes.md)的[初始化属性](../../../concepts/spatial-scenes.md#scene-initialization)之一，`defaultSize` 会影响空间场景刚创建完成时的大小；最终结果不一定与 `defaultSize` 中的值完全一致，因为要优先遵循操作系统的[空间计算](../../../concepts/spatial-computing.md)逻辑。

## 适用范围与设置方式 {#declared-in}

| 项目     | 内容                                                                         |
| -------- | ---------------------------------------------------------------------------- |
| 适用于   | `window` 类型场景支持 `width` 和 `height`；`volume` 类型场景还支持 `depth`。 |
| 新场景   | 通过 [`initScene`](../js-api/initScene.md) 设置。                            |
| 起始场景 | 通过 [Web App Manifest](../manifest-options/main-scene.md) 设置。            |
| 默认值   | 无需手动设置；未设置时会使用内置默认值（见[默认值](#default-value)）。       |

## 类型定义 {#type}

```ts title="defaultSize 类型"
{
  width?: number | string
  height?: number | string
  depth?: number | string
}
```

## 示例

### 使用 `initScene`

```js title="通过 initScene 设置新场景的 defaultSize" {6-11}
import { initScene } from "@webspatial/react-sdk";

initScene(
  "newSceneName",
  defaultConfig => {
    return {
      ...defaultConfig,
      defaultSize: {
        width: 500,
        height: "1000px",
        depth: "1m",
      },
    };
  },
  { type: "volume" },
);
window.open(newSceneUrl, "newSceneName");
```

### 使用 Web App Manifest

:::caution[Manifest 属性命名]
在 Web App Manifest 里，建议把 `defaultSize` 这样的驼峰属性名改成 `default_size` 这样的全小写下划线写法。
:::

```json title="通过 Web App Manifest 设置起始场景的 default_size" {5-8}
{
  "name": "example app",
  "start_url": "/",
  "xr_main_scene": {
    "default_size": {
      "width": 500,
      "height": "1000px"
    }
  }
}
```

## 默认值 {#default-value}

你不必手动设置 `defaultSize`。当场景创建时没有提供它，WebSpatial 运行时会套用内置的默认尺寸：

| 场景类型 | 内置默认 `defaultSize`                                                   |
| -------- | ------------------------------------------------------------------------ |
| `window` | `{ width: 1280, height: 720 }`（像素）                                   |
| `volume` | `{ width: "0.94m", height: "0.94m", depth: "0.94m" }`（0.94 m 的立方体） |

手动提供的 `defaultSize` 会覆盖对应场景的上述默认值。

## 可接受的值 {#accepted-values}

| 字段     | 可接受的值                                                            |
| -------- | --------------------------------------------------------------------- |
| `width`  | 数字（默认单位是 `px`）或带单位后缀的字符串，比如 `"100px"`、`"2m"`。 |
| `height` | 数字（默认单位是 `px`）或带单位后缀的字符串，比如 `"100px"`、`"2m"`。 |
| `depth`  | 数字（默认单位是 `px`）或带单位后缀的字符串，比如 `"100px"`、`"2m"`。 |

---
sidebar_position: 3
description: '在空间化元素、空间场景和 3D 内容使用的本地与全局坐标系之间转换坐标。'
---

# `convertCoordinate`

## 概述 {#summary}

在[不同坐标系](../../../concepts/3d-content-containers.md#2d-containing-3d)之间转换，包括：

- [空间场景容器](../../../concepts/spatial-scenes.md)的全局坐标系：基于 2D 布局系统，采用左手坐标系，原点都位于背板的左上角，Y 轴向下，Z 轴朝向用户，长度单位默认用面向 GUI 的 point 单位（`px`）
- [空间化 HTML 元素](../../../concepts/spatialized-html-elements.md)的本地坐标系：基于 2D 布局系统，采用左手坐标系，原点都位于 2D 面片的左上角，Y 轴向下，Z 轴朝向用户，长度单位默认用面向 GUI 的 point 单位（`px`）
- [3D entity](../react-components/Reality.md#3d-entity) 的本地坐标系：基于 3D 引擎系统，采用右手坐标系，原点在 3D 内容容器对应的局部 3D 空间的中心点，Y 轴朝上，Z 轴朝向用户，长度单位默认用面向现实世界物体的物理单位（`m`）

## 调用形式 {#signature}

```js
import { convertCoordinate } from "@webspatial/react-sdk";

const e2e = await convertCoordinate(position, {
  from: elementOrEntity,
  to: elementOrEntity,
});
const e2w = await convertCoordinate(position, {
  from: elementOrEntity,
  to: window,
});
const w2e = await convertCoordinate(position, {
  from: window,
  to: elementOrEntity,
});
```

## 参数 {#parameters}

### position

```ts
type CoordinateLike = { x: number; y: number; z: number };
```

要转换的位置点。
这个位置必须使用 `options.from` 所指定坐标系中的坐标值和默认单位来表达。

### options

- `options.from`：源坐标系。
- `options.to`：目标坐标系

## 返回结构 {#return-shape}

```ts
Promise<CoordinateLike>;
```

返回一个 Promise，resolve 后得到转换后的坐标点。如果底层桥接没有返回值，会原样返回传入的 `position`。

## 错误行为 {#error-behavior}

`convertCoordinate` 是异步且 fail-fast 的，在以下任一情况下会以 `WebSpatialRuntimeError` reject：

1. **运行时不支持**：当前运行时不支持 `convertCoordinate`（`supports('convertCoordinate')` 为 false），例如普通浏览器或缺少该能力的运行时。
2. **`from`/`to` 无效**：任一参数无法解析为可转换坐标的对象（`window`、空间化元素 ref、Entity ref 或 `<Model>` ref）。
3. **启动前调用**：`bootSpatial()` 尚未 resolve，还没有激活的空间场景。

请只在已启动的 WebSpatial 运行时中调用（在 [`<SpatialBoot>`](../react-components/SpatialBoot.md) 下渲染并按运行时检测做门控），并把 reject 当作硬错误处理，不要同步重试。

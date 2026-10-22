---
sidebar_position: 5
description: '通过同步能力探测查询当前运行时支持哪些空间能力，无需自行解析 userAgent。'
---

# `WebSpatialRuntime`

## 概述 {#summary}

`WebSpatialRuntime` 是从默认入口导出的一个小型命名空间，暴露同步能力探测函数。用它按运行时对空间功能做开关，而不是自己写 [userAgent 检测](../dom-api/userAgent.md)。

```js
import { WebSpatialRuntime } from "@webspatial/react-sdk";

if (WebSpatialRuntime.supports("Ornament")) {
  // 渲染实验性 ornament 界面。
}
```

探测结果根据检测到的运行时（visionOS、PicoOS 或 Puppeteer 测试环境）解析。在普通浏览器、服务器端，或检测不到任何空间运行时的环境中，所有探测都返回 `false`。

## 函数签名 {#signature}

```ts
WebSpatialRuntime.supports(name: string, tokens?: readonly string[]): boolean;
```

## 参数 {#parameters}

### name {#name}

要探测的能力键。未知名称（不在[能力键列表](#capability-keys)中的字符串）返回 `false`，而不是抛错。少量旧组件别名在查找前会被规范化：`Box` → `BoxEntity`、`Sphere` → `SphereEntity`、`Cone` → `ConeEntity`、`Cylinder` → `CylinderEntity`、`Plane` → `PlaneEntity`、`World` → `SceneGraph`。

### tokens {#tokens}

可选的子令牌，用于细化探测。每个子令牌都必须是 `name` 的文档化子令牌；只要有一个令牌未知、不是字符串、或当前运行时不支持，结果就是 `false`。多个子令牌之间是“逻辑与”关系——必须全部支持。

## 返回值 {#return}

`boolean`。

- `true`——当前运行时支持该能力（以及请求的所有子令牌）。
- `false`——当前运行时不支持、`name` 未知，或完全检测不到空间运行时。

在同一页面生命周期内重复调用结果保持一致；一旦运行时确定，能力集合被视为稳定（没有订阅或会话中途刷新 API）。

## 能力键 {#capability-keys}

| 分组           | 键                                                                                                                                                                                                                                                 |
| -------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 组件           | `Ornament`、`Model`、`Reality`、`Entity`、`BoxEntity`、`SphereEntity`、`ConeEntity`、`CylinderEntity`、`PlaneEntity`、`SceneGraph`、`ModelAsset`、`ModelEntity`、`UnlitMaterial`、`PBRMaterial`、`Material`、`AttachmentAsset`、`AttachmentEntity` |
| CSS API        | `-xr-background-material`、`-xr-back`、`-xr-depth`、`-xr-transform`                                                                                                                                                                                |
| 空间手势事件   | `SpatialTapEvent`、`SpatialDragStartEvent`、`SpatialDragEvent`、`SpatialDragEndEvent`、`SpatialRotateEvent`、`SpatialRotateEndEvent`、`SpatialMagnifyEvent`、`SpatialMagnifyEndEvent`                                                              |
| 动画（实验性） | `useAnimation`、`useEntityAnimation`                                                                                                                                                                                                               |
| JS 与场景 API  | `useMetrics`、`convertCoordinate`、`initScene`、`WindowScene`、`VolumeScene`                                                                                                                                                                       |
| DOM 读取       | `xrClientDepth`、`xrOffsetBack`、`xrInnerDepth`、`xrOuterDepth`                                                                                                                                                                                    |

## 已知子令牌 {#known-sub-tokens}

| `name`               | 文档化子令牌                                                                                                                                                                                                              |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Material`           | `unlit`、`pbr`                                                                                                                                                                                                            |
| `WindowScene`        | `defaultSize`、`resizability`                                                                                                                                                                                             |
| `VolumeScene`        | `defaultSize`、`resizability`、`worldScaling`、`worldAlignment`、`baseplateVisibility`                                                                                                                                    |
| `SpatialRotateEvent` | `constrainedToAxis`                                                                                                                                                                                                       |
| `AttachmentEntity`   | `placement`                                                                                                                                                                                                               |
| `Model`              | `autoplay`、`loop`、`stagemode`、`poster`、`loading`、`source`、`ready`、`currentSrc`、`boundingBoxCenter`、`boundingBoxExtents`、`entityTransform`、`paused`、`duration`、`playbackRate`、`play`、`pause`、`currentTime` |

## 类型 {#types}

默认入口还导出对应的 TypeScript 类型：

```ts
type CapabilityKey;
type WebSpatialRuntimeType = "visionos" | "picoos" | "puppeteer" | null;
type WebSpatialRuntimeSnapshot = {
  type: WebSpatialRuntimeType;
  shellVersion: string | null;
};
```

`WebSpatialRuntimeSnapshot` 描述了支撑 `supports()` 的运行时分类。它的存在让应用代码可以为自己针对特定运行时的集成做类型标注；文档化的入口点仍是探测函数本身。

## 各运行时下的行为 {#behavior-by-runtime}

- **普通浏览器 / SSR**——检测不到运行时，`supports()` 对任何键都返回 `false`。探测本身不会触发动态 import。
- **Puppeteer 测试环境**——被视为等价的空间运行时：`supports()` 对任何文档化能力键都返回 `true`，以便端到端测试真正加载空间 chunk。
- **visionOS / PicoOS**——能力根据运行时声明的 shell 版本解析。shell 版本未知或无法解析时，探测返回 `false`。

## 相关错误 {#related-errors}

在 `supports()` 为 `false` 时调用受运行时门控的 API，或在 [`bootSpatial()`](bootSpatial.md) 完成前调用它们，会抛出 `WebSpatialRuntimeError`——一个 `Error` 子类，暴露被拒绝的 `capability: string`。各 API 的回退约定见 [useMetrics](useMetrics.md) 和 [convertCoordinate](convertCoordinate.md)，引导阶段门控的变体见[实验性动画页面](../experimental/useAnimation.md)。

## 示例 {#examples}

按组件能力做开关：

```jsx
{WebSpatialRuntime.supports("Ornament") && <Ornament>...</Ornament>}
```

探测子令牌：

```js
const canConstrainRotation = WebSpatialRuntime.supports("SpatialRotateEvent", [
  "constrainedToAxis",
]);
```

读取 DOM 属性前先做防护：

```js
if (WebSpatialRuntime.supports("xrOuterDepth")) {
  console.log(window.xrOuterDepth);
}
```

## 相关 API {#related-apis}

- [`bootSpatial`](bootSpatial.md)——加载大多数受门控 API 所依赖的空间 chunk。
- [`userAgent`](../dom-api/userAgent.md)——本探测所基于的低层运行时检测。
- [实验性 API](../experimental/index.md)——`Ornament`、`useAnimation`、`useEntityAnimation` 都受能力探测门控。

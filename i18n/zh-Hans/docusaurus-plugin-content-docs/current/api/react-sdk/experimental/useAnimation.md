---
sidebar_position: 3
description: '通过声明式时间轴让空间化 2D 和 3D 容器动起来，来自实验性入口。'
---

# `useAnimation()`

## 概述 {#summary}

`useAnimation` 为[空间化 2D 或 3D 容器](../react-components/Model.md)（例如 `<Model>`）声明一条时间轴动画。它返回一个不透明的绑定对象，交给容器的 `xr-animation` 属性；以及播放 API 和当前值的 React style 出口。

```jsx
import { useAnimation } from "@webspatial/react-sdk/experimental";
```

:::caution[引导阶段门控]
在 [`bootSpatial()`](../js-api/bootSpatial.md) 完成前，`useAnimation` 会抛出命名 `useAnimation` 的 `WebSpatialRuntimeError`。把带动画的子树挂在 [`<SpatialBoot>`](../react-components/SpatialBoot.md) 内部。
:::

## 函数签名 {#signature}

```ts
useAnimation(config: SpatializedMotionConfig): UseAnimationResult;
```

```ts
type UseAnimationResult = readonly [
  animation: SpatializedMotionBinding,
  api: SpatializedPlaybackApi,
  style: CSSProperties,
];
```

## 配置 {#config}

`SpatializedMotionConfig` 接受单段或百分比关键帧时间轴：

```ts
// 单段：from → to
{
  from: { opacity: 1, transform: { translate: { x: 0 } } },
  to: { opacity: 0.2, transform: { translate: { x: 120 } } },
  duration: 0.6,          // 秒
  timingFunction: "easeInOut", // linear | easeIn | easeOut | easeInOut
  delay: 0,
  autoStart: true,
  loop: false,            // 或 true，或 { reverse: true }
  playbackRate: 1,
  onStart, onComplete, onStop, onReset, onError,
}

// 时间轴：百分比关键帧记录
{
  duration: 0.6,
  timeline: {
    "0%":   { opacity: 1, transform: { translate: { x: 0 } } },
    "100%": { opacity: 0.2, transform: { translate: { x: 120 } } },
  },
}
```

可动画的视觉值：`opacity`，以及逐轴的 `transform.translate` / `transform.rotate` / `transform.scale`（平移单位为 CSS 像素，旋转单位为度，缩放无量纲——与对应的 CSS 一致）。不支持任意 CSS transform 字符串、skew、perspective 和矩阵插值。

## 返回值 {#return}

### animation（绑定） {#animation-binding}

不透明的绑定对象，传给容器的 `xr-animation` 属性：

```jsx
<Model src={modelUrl} xr-animation={animation} style={style} />
```

在普通浏览器中，回退宿主不会绑定该对象——页面退化为静态 HTML。

### api {#api}

```ts
interface SpatializedPlaybackApi {
  play(): void;
  pause(): void;
  stop(): void;
  reset(): void;
  finish(): void;
  readonly isAnimating: boolean;
  readonly isPaused: boolean;
  readonly finished: boolean;
  readonly playState: "idle" | "queued" | "running" | "paused" | "finished";
}
```

### style {#style}

由动画当前视觉值派生的 React `CSSProperties` 出口。展开到容器上后，中间帧数值也能渲染到网页回退宿主上。

## 示例 {#example}

```jsx
import { SpatialBoot } from "@webspatial/react-sdk";
import { Model } from "@webspatial/react-sdk";
import { useAnimation } from "@webspatial/react-sdk/experimental";

function AnimatedModel() {
  const [animation, api, style] = useAnimation({
    from: { transform: { translate: { z: 0 } } },
    to: { transform: { translate: { z: 200 } } },
    duration: 1.2,
    loop: { reverse: true },
  });

  return (
    <div>
      <button onClick={() => api.pause()}>暂停</button>
      <Model src="/models/chair.glb" xr-animation={animation} style={style} />
    </div>
  );
}
```

## 相关 API {#related-apis}

- [`useEntityAnimation`](./useEntityAnimation.md)——用于 3D 实体的时间轴动画。
- [实验性 API 索引](./index.md)——入口约定与共同门控行为。
- [`<Model>`](../react-components/Model.md)——消费 `xr-animation` 的容器。

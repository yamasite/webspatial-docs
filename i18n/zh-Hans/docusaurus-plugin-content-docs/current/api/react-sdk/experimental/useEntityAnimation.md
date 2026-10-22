---
sidebar_position: 4
description: '通过声明式变换时间轴让 3D 实体动起来，来自实验性入口。'
---

# `useEntityAnimation()`

## 概述 {#summary}

`useEntityAnimation` 为 3D [Entity](../react-components/Reality.md#3d-entity) 声明一条变换时间轴动画。它返回一个动画句柄，展开到实体的 `animation` 属性上；以及播放 API 和实体已确认的变换。

```jsx
import { useEntityAnimation } from "@webspatial/react-sdk/experimental";
```

:::caution[引导阶段门控]
在 [`bootSpatial()`](../js-api/bootSpatial.md) 完成前，`useEntityAnimation` 会抛出命名 `useEntityAnimation` 的 `WebSpatialRuntimeError`。把带动画的子树挂在 [`<SpatialBoot>`](../react-components/SpatialBoot.md) 内部。
:::

## 函数签名 {#signature}

```ts
useEntityAnimation(config: EntityMotionConfig): UseEntityAnimationResult;
```

```ts
type UseEntityAnimationResult = [
  animation: EntityMotionAnimation,
  api: EntityPlaybackApi,
  entityProps: EntityMotionProps,
];
```

## 配置 {#config}

```ts
{
  // 稀疏的起止姿态。单位：position 为米，rotation 为度，scale 为无量纲倍率。
  from: { position: { x: 0 }, rotation: { y: 0 } },
  to: { position: { x: 1 }, rotation: { y: 90 } },

  // 或使用百分比关键帧时间轴（优先于顶层起止）：
  timeline: {
    from: { position: { x: 0 } },
    "50%": { position: { x: 0.5 }, timingFunction: "easeInOut" },
    to: { position: { x: 1 } },
  },

  duration: 1,          // 秒
  timingFunction: "easeInOut", // linear | easeIn | easeOut | easeInOut
  delay: 0,
  playbackRate: 1,
  loop: false,          // 或 true，或 { reverse: true }
  autoStart: true,

  onStart, onComplete, onStop, onReset, // 收到 EntityMotionProps（已确认姿态）
  onError, // 收到 EntityPlaybackError { code, reason }
}
```

## 返回值 {#return}

### animation {#animation}

传给 `<Entity>` 的 `animation` 属性：

```jsx
<Entity animation={animation} />
```

### api {#api}

在[标准播放控制](./useAnimation.md#api)（`play`、`pause`、`stop`、`reset`、`finish`，以及 `isAnimating` / `isPaused` / `finished` / `playState` 读取器）之上扩展：

```ts
set(update: EntityMotionPatch): void; // 提交一次稀疏变换更新；
                                        // 确认值通过 entityProps 返回。
```

### entityProps {#entityprops}

运行时最近一次确认的完整变换：

```ts
{
  position?: Vec3; // 米
  rotation?: Vec3; // 度
  scale?: Vec3;    // 无量纲倍率
}
```

## 示例 {#example}

```jsx
import { SpatialBoot } from "@webspatial/react-sdk";
import { Reality, Entity } from "@webspatial/react-sdk";
import { useEntityAnimation } from "@webspatial/react-sdk/experimental";

function SpinningEntity() {
  const [animation, api] = useEntityAnimation({
    from: { rotation: { y: 0 } },
    to: { rotation: { y: 360 } },
    duration: 3,
    loop: true,
  });

  return (
    <SpatialBoot>
      <Reality>
        <Entity animation={animation} />
      </Reality>
    </SpatialBoot>
  );
}
```

## 相关 API {#related-apis}

- [`useAnimation`](./useAnimation.md)——用于空间化容器的时间轴动画。
- [实验性 API 索引](./index.md)——入口约定与共同门控行为。
- [`<Reality>`](../react-components/Reality.md)——声明实体及其属性（含 `animation`）。

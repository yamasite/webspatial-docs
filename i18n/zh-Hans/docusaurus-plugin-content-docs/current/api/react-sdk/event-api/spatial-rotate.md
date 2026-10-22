---
sidebar_position: 4
description: '处理双手旋转手势，并读取 3D 空间中的旋转结果。'
---

# 空间旋转（Spatial Rotate）

## 概述 {#summary}

表示完成了一次对空间目标的双手「选择、激活、持续」动作，并产生旋转效果。无论这种交互是[通过间接的「注视 + 捏合」完成，还是通过直接触摸完成](../../../concepts/natural-interactions.md)。

## 触发条件 {#trigger-conditions}

| 目标                                                                  | 触发时机                                                         |
| --------------------------------------------------------------------- | ---------------------------------------------------------------- |
| [空间化 2D HTML 元素](../../../concepts/spatialized-html-elements.md) | 在自身内容占据的 3D 空间位置被「捏住不放」后触发这套事件。       |
| [3D 容器元素](../../../concepts/3d-content-containers.md)             | 在自身可交互内容占据的 3D 空间位置被「捏住不放」后触发这套事件。 |

## 心智模型 {#mental-model}

双手之间的连线（相当于一个真实物体）可以在 3D 空间中做任意角度的旋转。

## 事件类型名称 {#event-type-signature}

| 阶段       | DOM 事件           |
| ---------- | ------------------ |
| 旋转持续中 | `spatialrotate`    |
| 旋转结束   | `spatialrotateend` |

## React 用法 {#react-usage}

| 阶段       | JSX 属性             |
| ---------- | -------------------- |
| 旋转持续中 | `onSpatialRotate`    |
| 旋转结束   | `onSpatialRotateEnd` |

## 原生 DOM 用法 {#native-dom-usage}

:::info[当前限制]
[WebSpatial SDK](../../../introduction/getting-started.md#webspatial-sdk) 现阶段不允许在 DOM 元素（包括来自 Ref 的元素）上直接监听空间事件。
:::

## 事件生命周期 {#event-lifecycle}

「选择」过程中不触发事件；捏住不放激活后，保持动作会持续触发 `spatialrotate`，松开后触发 `spatialrotateend`。

## SpatialRotateEvent 事件数据 {#spatialrotateevent-payload}

| 字段         | 取值     | 含义                                           |
| ------------ | -------- | ---------------------------------------------- |
| `quaternion` | 四元数。 | 相对于初始状态的旋转量，其中包含旋转轴的信息。 |

## SpatialRotateEndEvent 事件数据 {#spatialrotateendevent-payload}

:::info[没有额外数据]
`spatialrotateend` 回调拿到的 `SpatialRotateEndEvent` 对象没有额外属性。
:::

## `spatialEventOptions`：约束旋转轴 {#spatialeventoptions}

在 `<SpatialDiv>`、`<Model>` 和 Reality `<Entity>` 上，可以通过 `spatialEventOptions` 把旋转手势约束到单一轴：

```tsx
<SpatialDiv
  onSpatialRotate={e => /* ... */}
  spatialEventOptions={{ constrainedToAxis: [0, 1, 0] }}
/>
```

```ts
type SpatialEventOptions = {
  /** 方向向量；省略或传 [0, 0, 0] 表示不约束。 */
  constrainedToAxis?: Vec3 | readonly [number, number, number];
};
```

- 省略 `constrainedToAxis`（或传 `[0, 0, 0]`）即不约束旋转。
- 对于 `<SpatialDiv>` 和 `<Model>`，轴向量在目标施加变换前的父级坐标轴中解释；对于 Reality 实体，在 Reality 视图/场景坐标轴中解释。返回的 `quaternion` 始终是父级局部坐标。
- 轴约束旋转是运行时的子能力：依赖它之前先用 `supports("SpatialRotateEvent", ["constrainedToAxis"])` 做门控；不支持该子能力的运行时会回退为不约束的旋转。

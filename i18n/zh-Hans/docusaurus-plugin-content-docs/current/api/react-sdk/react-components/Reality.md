---
sidebar_position: 3
description: '使用统一渲染的 3D 引擎 API，在空间化容器中渲染动态 3D 内容。'
---

# `<Reality>`

## 概述 {#overview}

`<Reality>` 跟同为 [3D 内容容器元素](../../../concepts/3d-content-containers.md)的 [`<Model>`](./Model.md) 一样具备[空间化 HTML 元素的能力](../../../concepts/spatialized-html-elements.md)，作为空间中悬浮的 2D 面片使用，参与 HTML/CSS 布局，跟`<Model>` 的区别是，`<Reality>` 是[动态 3D 内容容器](../../../concepts/3d-content-containers.md#dynamic-3d-containers)，它的 3D 内容不是用预先制作好的、静态的 3D 模型文件来实现，而是在 2D 面片前方的局部空间中可以用[支持统一渲染的 3D Engine API](../../../concepts/3d-content-containers.md#3d-engine-api) 动态渲染任意 3D 内容。

这些 3D 引擎 API 被 WebSpatial SDK 作为 React 组件提供：

```js
import {
  Reality,
  Material,
  ModelAsset,
  AttachmentAsset,
  World,
  Entity,
  Box,
  Sphere,
  Plane,
  Cone,
  Cylinder,
  ModelEntity,
  AttachmentEntity,
} from "@webspatial/react-sdk";
```

```js
<Reality style={{ width: "500px", height: "500px", "--xr-depth": 100 }}>
  <Material type="unlit" id="red" color="#ff0000" />
  <ModelAsset id="teapot" src="https://example.com/model.usdz" />
  <World>
    <Box materials={["red"]} width={0.2} height={0.2} depth={0.2} />
  </World>
</Reality>
```

## 场景图 {#scene-graph}

这些 3D 引擎 API 包含两类：

第一类是 [3D Entity](#3d-entity)，这种 React 组件只能在 `<World>`（也可写作 `<SceneGraph>`）里使用，`<World>` 是动态 3D 容器内所有 3D 内容的根节点。

第二类是 [3D 资产的声明](#3d-assets)，比如材质（`<Material>`），这种 React 组件只能作为 `<Reality>` 中的顶层子节点、在 `<World>` 外面使用，需要被 3D Entity 引用才能实际影响渲染结果。

## 3D 资产 {#3d-assets}

在 `<Reality>` 的顶层子节点中，可以包含以下 3D 资产声明：

1. 可以引用预先声明的材质。

```js
<Reality>
  <Material type="unlit" id="solid" color="#00ff00" />
  <Material type="unlit" id="glass" color="#0000ff" transparent opacity={0.5} />
  <World>
```

除了联合形式 `<Material type="unlit" | "pbr" ... />` 外，还导出了两个独立的材质组件和一个纹理组件，都通过 `id` 注册，再由实体的 `materials` 列表引用：

- `<UnlitMaterial id="..." {...options} />`：不受场景光照影响的平光材质。
- `<PBRMaterial id="..." {...options} />`：响应场景光照的基于物理的材质。`roughness`（`0` = 镜面光滑，`1` = 完全粗糙）与 `metalness`（`0` = 非金属，`1` = 全金属）约定与 Three.js `MeshStandardMaterial` 一致，所有属性都是响应式的。
- `<Texture id="..." url="..." onLoad onError />`：绑定到材质的纹理资源。

`<PBRMaterial>` 还提供纯数据的 `MaterialPresets` 预设，可以展开后再覆盖单个属性：

```jsx
import { PBRMaterial, MaterialPresets } from "@webspatial/react-sdk";

<PBRMaterial id="car" {...MaterialPresets.metal} color="#C0C0C0" />
```

`MaterialPresets` 内置 `matte`、`glossy`、`plastic`、`metal`、`glass`。预设不是原生概念，新增或调整预设都不需要重新构建原生层。

2. 可以声明 3D 模型文件。

```js
<Reality>
  <ModelAsset
    id="ship-blueprint"
    src="https://example.com/fighter.usdz"
    onLoad={() => {}}
    onError={() => {}} />
  <World>
```

3. 可以声明要附着在 Entity 上的 2D HTML/CSS 内容。

```js
<Reality>
  <AttachmentAsset name="info">
    <div style={{ width: '100%' }}>
      <p>Some text</p>
    </div>
  </AttachmentAsset>
  <World>
```

## 3D 实体 {#3d-entity}

3D Entity 组件不参与 HTML 布局，只在 `<Reality>` 容器内按照 3D 引擎体系来渲染，它们不支持 CSS 样式，而是采用 3D 引擎体系里的「Transform 属性」：

```js
<Entity
  // Position: x (left/right), y (down/up), z (away/toward)
  position={{ x: 0.1, y: -0.2, z: 0.3 }}

  // Rotation: radians (Math.PI = 180°)
  rotation={{ x: 0, y: Math.PI / 2, z: 0 }}  // 90° on Y-axis

  // Scale: 1 = normal, 2 = double, 0.5 = half
  scale={{ x: 1, y: 2, z: 1 }}  // stretched vertically
  >
```

Transform 属性默认使用 `<Reality>` 对应的 2D 面片前方局部 3D 空间的坐标系，原点是这个空间的中心点。采用右手坐标系，Y 轴朝上，Z 轴朝向用户，长度单位默认用面向现实世界物体的物理单位（`m`）。

:::tip[相关 API]
这个空间的深度可以用 [`depth`](../css-api/depth.md) 设置，可以用 [`clientDepth`](../dom-api/clientDepth.md) 查询当前的深度。
:::

对于 `<World>` 顶层的 Entity 节点，Transform 属性中 `position` 的值是相对于坐标系原点的，对于其他作为子节点的 Entity，Transform 属性中 `position` 的值是相对于父 Entity 的 `position`。

除 `position`、`rotation`、`scale` 外，`<Entity>` 还支持：

| 属性          | 含义                                                                                                                                                       |
| ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `id`          | 实体的稳定字符串标识。                                                                                                                                     |
| `name`        | 实体的可读名称。                                                                                                                                           |
| `enableInput` | 为 `true` 时，该实体可以成为空间手势的目标（`onSpatialTap`、`onSpatialDrag`、`onSpatialRotate`、`onSpatialMagnify` 及其 start/end 变体），默认为 `false`。 |
| `animation`   | 由 `useEntityAnimation()` 返回、用于驱动实体变换的动画绑定。                                                                                               |

手势回调（`onSpatialTap` 等）与 `spatialEventOptions` 的载荷形状与[空间交互事件](../event-api/spatial-tap.md)一致，按 Reality 场景的局部坐标系解释。

WebSpatial SDK 目前提供的[开箱即用的 Entity](../../../concepts/3d-content-containers.md#3d-engine-api) 有以下几类：

### 基础实体 {#base-entity}

`<Entity>` 不可见，作为其他 Entity 的父组件和 group container 使用，可以用它把多个 Entity 包含在一个 group 里。

```js
<Reality>
  <World>
    <Entity position={{ x: 1, y: 0, z: 0 }}>
      <Box />
      <Box />
    </Entity>
    <Box position={{ x: 2, y: 0, z: 0 }} />
  </World>
```

### 几何实体 {#primitive-entities}

几何实体（primitive）包括以下几何形状，它们各自有不同的额外属性：

- `<Box>`
  - 属性：`width`,`height`,`depth`, `cornerRadius`
- `<Plane>`
  - 属性：`width`,`height`, `cornerRadius`
- `<Sphere>`
  - 属性：`radius`
- `<Cone>`
  - 属性：`height`, `radius`
- `<Cylinder>`
  - 属性：`height`, `radius`

示例：

```js
<Box
  width={0.2}
  height={0.2}
  depth={0.2} // meters (0.1 = 10cm)
  cornerRadius={0.01} // rounded edges
/>
```

这些几何实体都支持 `materials` 属性，可以引用[预先声明的材质](#3d-assets)。

```js
<Reality>
  <Material type="unlit" id="solid" color="#00ff00" />
  <Material type="unlit" id="glass" color="#0000ff" transparent opacity={0.5} />
  <World>
    <Box
      width={0.2}
      height={0.2}
      depth={0.2} // meters (0.1 = 10cm)
      materials={["glass"]}
      cornerRadius={0.01} // rounded edges
    />
  </World>
</Reality>
```

### 模型实体 {#model-entity}

`<ModelEntity>` 是用预制好的 3D 模型文件渲染内容的 Entity。

以「飞船舰队」为例：将飞船模型下载和加载到内存中一次，然后引用它生成 3 个独立的 Model Entity，渲染出 3 艘飞船

```js
import { Reality, World, ModelAsset, ModelEntity } from "@webspatial/react-sdk";

function SpaceshipFleet() {
  return (
    <Reality style={{ width: "100%", height: "500px" }}>
      {/* --- 1. THE RESOURCE --- */}
      {/* This downloads the file once. It is INVISIBLE right now. */}
      <ModelAsset
        id="ship-blueprint"
        src="https://example.com/fighter-jet.usdz"
      />

      {/* --- 2. THE SCENE --- */}
      <World>
        {/* Leader Ship: Center, Normal Size */}
        <ModelEntity
          model="ship-blueprint" // Points to the ID above
          position={{ x: 0, y: 0, z: 0 }}
          scale={{ x: 1, y: 1, z: 1 }}
        />

        {/* Left Wingman: Moved left, slightly smaller */}
        <ModelEntity
          model="ship-blueprint" // Reuses the same loaded file!
          position={{ x: -0.5, y: -0.2, z: 0.3 }}
          scale={{ x: 0.8, y: 0.8, z: 0.8 }}
        />

        {/* Right Wingman: Moved right, slightly smaller */}
        <ModelEntity
          model="ship-blueprint" // Reuses the same loaded file
          position={{ x: 0.5, y: -0.2, z: 0.3 }}
          scale={{ x: 0.8, y: 0.8, z: 0.8 }}
        />
      </World>
    </Reality>
  );
}
```

对于动画需求，可以用 JS 轮询修改 Transform 属性来实现。
示例：

```js
const [rotation, setRotation] = useState({ x: 0, y: 0, z: 0 });

useEffect(() => {
  let id;
  function animate() {
    setRotation(prev => ({ ...prev, y: prev.y + 0.02 }));
    id = requestAnimationFrame(animate);
  }
  animate();
  return () => cancelAnimationFrame(id);
}, []);

<Box rotation={rotation} />;
```

## 附着实体 {#attachment-entity}

`<AttachmentEntity>` 是一个类似 `<Plane>` 的 Entity，可以引用[预先声明好的 2D HTML/CSS 内容](#3d-assets)，让它附着在自己表面上。

它支持以下属性：

| 属性                            | 含义                                                            |
| ------------------------------- | --------------------------------------------------------------- |
| `attachment`                    | 要附着的 [`<AttachmentAsset>`](#3d-assets) 的 `name`，必填。    |
| `position`、`rotation`、`scale` | [Transform 属性](#3d-entity)，单位为米 / 弧度。                 |
| `width`、`height`               | 附着表面尺寸，单位为米。                                        |
| `cornerRadius`                  | 圆角半径，单位为米；非有限值或负值会回退为 `0`。                |
| `backgroundMaterial`            | 应用于附着表面的[背景材质](../css-api/background-material.md)。 |

没有 `size` 属性；表面尺寸用 `width`/`height` 设置。

```js
<Reality>
  <AttachmentAsset name="info">
    <div style={{ width: "100%" }}>
      <p>Some text</p>
    </div>
  </AttachmentAsset>
  <World>
    <Box position={{ x: 0.5, y: -0.2, z: 0.3 }} />
    <Entity position={{ x: -0.5, y: -0.2, z: 0.3 }}>
      <AttachmentEntity
        attachment="info"
        position={{ x: 0, y: 0, z: 0 }}
        width={0.1}
        height={0.1}
        cornerRadius={0.01}
      />
    </Entity>
  </World>
</Reality>
```

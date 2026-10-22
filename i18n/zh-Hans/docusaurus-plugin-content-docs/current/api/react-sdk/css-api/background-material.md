---
sidebar_position: 3
description: '为场景或元素的背板设置透明或实时渲染的半透明材质背景。'
---

# `background-material`

## 概述 {#summary}

`background-material` 是 WebSpatial 在现有 Web 标准基础上新增的 CSS 属性，让[悬浮在空间中的网页窗口](../../../concepts/spatial-scenes.md)和[空间化 HTML 元素](../../../concepts/spatialized-html-elements.md)都能完全透明或能使用实时渲染的半透明材质作为背景，而不仅限于固定的不透明颜色。

## 适用对象 {#applies-to}

WebSpatial SDK 暂时只支持在 HTML 元素（代表网页窗口本身）和被[标记为空间化 HTML 元素](../react-components/jsx-marker.md)的 2D HTML 元素（不含 [3D 容器元素](../../../concepts/3d-content-containers.md)）上使用 `background-material`。

## 心智模型 {#mental-model}

该属性在两类对象上的行为不同：

- 对网页窗口，`none` 保持原生不透明背板；`transparent` 完全去掉背板，让内部元素漂浮在空间中；半透明值则把背板替换成随视角和环境实时渲染的材质。
- 对空间化 HTML 元素，`none` 和 `transparent` 都表示"无背板"；半透明值会新增一块背板。

## 语法 {#syntax}

:::caution[标准化完成前的临时 CSS 命名]
WebSpatial API 中的 CSS 属性在标准化完成前，需要加上 `-xr-` 前缀。
在 WebSpatial SDK 当前的实现中，出于性能考虑，是用 CSS 自定义变量来实现新的 CSS API，因此 `background-material` 的属性名在 CSS 样式和 `style` 属性里都要写成 `--xr-background-material`。
:::

示例：用于网页窗口

```css
html {
  --xr-background-material: transparent;
}
```

示例：用于空间化 HTML 元素

```css
div {
  position: absolute;
  --xr-back: 100px;
  --xr-background-material: translucent;
}
```

## 值语法 {#value-grammar}

`background-material` 的值是枚举值：

- `none` — 默认值。网页窗口保持原生不透明背景；空间化 HTML 元素背景透明。
- `translucent` — 玻璃质感的半透明材质。
- `thick` — 更厚的半透明材质。
- `regular` — 普通（中等）厚度的半透明材质。
- `thin` — 更薄的半透明材质。
- `transparent` — 完全透明、无底板的背景。

## 初始值 {#initial-value}

初始值相当于：

```css
--xr-background-material: none;
```

## 是否继承 {#inherited}

子元素不会继承父元素的 `background-material` 属性。

## 回退行为 {#fallback-behavior}

在不支持 WebSpatial 的环境里，`background-material` 会被自动忽略。

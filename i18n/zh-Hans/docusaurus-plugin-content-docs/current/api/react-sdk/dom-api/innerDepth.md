---
sidebar_position: 4
description: '通过 DOM 接口读取空间场景深度：window.xrInnerDepth 与 window.xrOuterDepth。'
---

# `innerDepth`

## 概述 {#summary}

读取 [volume 类型空间场景容器](../../../concepts/spatial-scenes.md)的深度/厚度。

同一 DOM 深度分组还暴露一个配套的 window 读取属性 [`xrOuterDepth`](#xrouterdepth)。

## 可用对象 {#exposed-on}

`window` 对象上可以访问这个属性。

## 心智模型 {#mental-model}

跟 `window.innerHeight` 一样：只读，不含网页之外窗口原生 UI 的高度。

## 语法 {#syntax}

:::caution[标准化完成前的临时 DOM 命名]
WebSpatial API 中的 DOM 属性在标准化完成前，需要加上 `xr` 前缀并把原有的首字母改成大写，因此 `innerDepth` 的属性名要写成 `xrInnerDepth`。
:::

## 读写语义 {#read--write-semantics}

只读。

示例：

```js
const currentVolumeDepth = window.xrInnerDepth;
```

## `xrOuterDepth` {#xrouterdepth}

### 概述 {#xrouterdepth-summary}

与 `xrInnerDepth` 同处 `window` 对象的配套读取属性。

### 可用对象 {#xrouterdepth-exposed-on}

`window` 对象上可以访问这个属性。

## 读写语义 {#xrouterdepth-read--write-semantics}

只读。与 `xrInnerDepth` 一样，它必须带 `xr` 前缀书写：

```js
const outerDepth = window.xrOuterDepth;
```

这两个读取属性在 `window` 上都是可选的：只有当 [`WebSpatialRuntime.supports()`](../js-api/WebSpatialRuntime.md) 报告对应能力时——分别为 `supports("xrInnerDepth")` 和 `supports("xrOuterDepth")`——它们才存在，否则为 `undefined`。读取前请先做防护：

```js
if (WebSpatialRuntime.supports("xrOuterDepth")) {
  console.log(window.xrOuterDepth);
}
```

支持情况因运行时而异：例如 picoOS `PicoWebApp/0.1.1` 和 `0.1.2` 的能力行报告两个深度读取属性均不支持，因此这些属性在那里保持 `undefined`。

## 回退行为 {#fallback-behavior}

在不支持 WebSpatial 的环境里，`innerDepth` 和 `outerDepth` 都不存在。

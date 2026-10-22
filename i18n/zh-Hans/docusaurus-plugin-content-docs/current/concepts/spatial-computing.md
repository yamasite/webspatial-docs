---
sidebar_position: 1
description: '理解空间计算与传统 XR 的区别，以及空间应用、运行时和统一渲染等基础概念。'
---

# 空间计算 {#spatial-computing}

空间计算是指一种计算设备和操作系统的新能力，是传统 XR 设备和 XR OS（包括[虚拟现实、混合现实或增强现实](https://developer.picoxr.com/document/web/introduce-xr/)）的进一步发展。

传统 XR OS 会持续跟踪空间环境中的物体和用户自身，获得它们的 3D 位置和姿态数据，软件界面和虚拟数字内容可以利用这些底层数据，跟空间环境做动态结合——比如固定在空间中某个位置，同时始终正面朝向用户，或自动跟随用户的手腕，始终在手腕旁边显示。但传统 XR OS 没有明确软件具体如何跟空间结合，需要各个 XR 应用利用 OS 提供的底层基础数据，自行设计和实现不同的结合方式和效果。

空间计算操作系统（Spatial OS）统一实现了这种软件跟空间的结合，在 OS 内部做大量跟空间结合的计算，把这种空间计算得到的新能力（比如[空间化 UI](../introduction/getting-started.md#webspatial-api)、2D 和 3D 混合共存）提供给[空间应用](#spatial-app)，对这些应用做[统一渲染](#unified-rendering)。

[visionOS](https://developer.apple.com/visionos/) 和 [PICO OS 6](https://developer.picoxr.com/document/discover/pico-os-6-overview/) 都属于这种 Spatial OS。

## 统一渲染 {#unified-rendering}

统一渲染的目的是让 Spatial OS 能为各个软件统一做[空间计算](./spatial-computing.md)，让多个[空间应用](#spatial-app)的 2D 和 3D 内容能在同一个空间里共存，能融合到同一个坐标系和光照环境中（比如具备位置关系和遮挡、阴影等效果）。

为此，各个空间应用不能孤立地渲染自己的内容、随意实现交互，需要通过[操作系统统一管理的 2D/3D 内容容器（称为空间场景）](./spatial-scenes.md)来提供应用内容，通过[操作系统可理解的 API](./3d-content-containers.md#3d-engine-api) 来实现这些内容，从而让操作系统能尽可能理解各个应用中的内容，实现统一渲染，让这些内容具备一致的跟空间结合和自然交互的能力。

## Spatial Runtime {#spatial-runtime}

为了持续做[空间计算](./spatial-computing.md)和[统一渲染](#unified-rendering)，Spatial OS 需要运行单一的 3D 空间和单一的 3D 渲染引擎，相当于一个负责运行所有[空间应用](#spatial-app)的 Runtime，所有空间应用都在这个 3D 空间中运行，都由这个 3D 引擎渲染。

这个 3D 空间在多个空间应用共存的模式下，称作 Shared Space，而如果隐藏其他空间应用，把整个空间都留给一个空间应用使用，则称作 Full Space。

在 Shared Space 里，空间应用默认获取不到眼动、手部移动、空间环境信息等隐私数据，需要通过用户授权进入了 Full Space 模式、独占整个空间的情况下，空间应用才能获得这些数据，实现自定义的空间计算。

## 空间应用 {#spatial-app}

空间应用是指在 [Shared Space 或 Full Space](#spatial-runtime) 里运行、由操作系统做[统一渲染](#unified-rendering)、自动具备[空间计算](./spatial-computing.md)能力的应用。

空间应用采用「[2D 包含 3D](./3d-content-containers.md#2d-containing-3d)」的开发范式，现有的 2D 应用（比如 [Web App](./webspatial-app.md#web-app)）只要能使用[新增的空间计算 API（比如 WebSpatial API）](../introduction/getting-started.md#webspatial-api)，就可以成为空间应用，可以按需添加相应的代码，让 2D 内容进入 3D 空间、增加有真实体积的 3D 内容跟 2D 内容混合使用、让内容支持空间交互和跟空间环境结合。

如果不使用空间计算 API，2D 应用的 UI 不会自动被空间化和 3D 化，不会自动成为空间应用。

基于 OpenXR 和底层 3D 图形 API 的应用（包括网页里的 WebXR 内容）不属于空间应用，因为它们需要独立渲染自身、独占整个空间，无法跟其他应用共存在同一空间、无法复用 Spatial OS 的全部空间计算能力。

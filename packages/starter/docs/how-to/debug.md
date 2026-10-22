<!--
sidebar_position: 7
description: 'Install console helpers to inspect the live spatial scene and spatialized elements during development.'
-->

# Debug the spatial scene

## Summary

`enableDebugTool()` installs two `window` helpers that let you inspect the live spatial scene and reach spatialized 2D elements from the console:

```js
import { enableDebugTool } from "@webspatial/react-sdk";
```

On the server (`window` undefined) the call is a no-op.

## Prerequisites

The helpers do real work against an active spatial session, so call `enableDebugTool()` after [`bootSpatial()`](../api/react-sdk/js-api/bootSpatial.md) has resolved — for example, from inside a [`<SpatialBoot>`](../api/react-sdk/react-components/SpatialBoot.md) success state or after an imperative `await bootSpatial()`. Invoking the scene helper before boot throws an error explaining that the session does not exist yet.

## Install the helpers

```js
import { bootSpatial, enableDebugTool } from "@webspatial/react-sdk";

async function setupDebugTools() {
  await bootSpatial();
  enableDebugTool();
}
```

## `window.inspectCurrentSpatialScene()`

An async function that returns the result of inspecting the current spatial scene:

```js
await window.inspectCurrentSpatialScene();
```

It throws `inspectCurrentSpatialScene: no spatial session…` when no spatial session exists yet (boot not resolved).

## `window.getSpatialized2DElement(el)`

Returns the internal spatialized element backing a spatialized 2D DOM node, for direct inspection:

```js
const el = document.querySelector("[enable-xr]");
const spatialized = window.getSpatialized2DElement(el);
```

## Companion overlay

For a visual inspector, render the experimental [`<WebSpatialInspector>`](../api/react-sdk/experimental/index.md#webspatialinspector) from `@webspatial/react-sdk/experimental` — an ornament-based overlay for inspecting the live scene.

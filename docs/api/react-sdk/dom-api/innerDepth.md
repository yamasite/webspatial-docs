---
sidebar_position: 4
description: 'Read Spatial Scene depth through the DOM interface: window.xrInnerDepth and window.xrOuterDepth.'
---

# `innerDepth`

## Summary

Reads the depth/thickness of a [Spatial Scene container of type Volume](../../../concepts/spatial-scenes.md).

The same DOM depth group also exposes a companion window readback, [`xrOuterDepth`](#xrouterdepth).

## Exposed On

This property is available on the `window` object.

## Mental Model

Like `window.innerHeight`: read-only and does not include the height of native window UI outside the webpage.

## Syntax

:::caution[Temporary prefixed DOM name]
Before standardization is complete, DOM properties in the WebSpatial API need the `xr` prefix and the original first letter becomes uppercase, so `innerDepth` must be written as `xrInnerDepth`.
:::

## Read / Write Semantics

Read-only.

Example:

```js
const currentVolumeDepth = window.xrInnerDepth;
```

## `xrOuterDepth`

### Summary

`xrOuterDepth` is the companion window-level depth readback in the same DOM depth group as `xrInnerDepth`.

### Exposed On

This property is available on the `window` object.

## Read / Write Semantics

Read-only. Like `xrInnerDepth`, it must be written with the `xr` prefix:

```js
const outerDepth = window.xrOuterDepth;
```

Both readbacks are optional on `window`: they are present only when [`WebSpatialRuntime.supports()`](../js-api/WebSpatialRuntime.md) reports the matching capability — `supports("xrInnerDepth")` and `supports("xrOuterDepth")` respectively — and are `undefined` otherwise. Guard the read before using it:

```js
if (WebSpatialRuntime.supports("xrOuterDepth")) {
  console.log(window.xrOuterDepth);
}
```

Support varies by runtime: for example, the picoOS capability rows for `PicoWebApp/0.1.1` and `0.1.2` report both depth readbacks as unsupported, so the properties stay `undefined` there.

## Fallback Behavior

In environments that do not support WebSpatial, `innerDepth` and `outerDepth` do not exist.

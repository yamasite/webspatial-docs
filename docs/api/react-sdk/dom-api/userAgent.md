---
sidebar_position: 1
description: 'Inspect the current device, operating system, and whether the app is running in WebSpatial mode.'
---

# `userAgent`

## Summary

Obtains information about the current device, operating system, and runtime environment, including information about the [Web Runtime](../../../concepts/webspatial-app.md#web-runtime) and [WebSpatial Runtime](../../../concepts/webspatial-app.md#webspatial-runtime).

:::tip[Use capability queries for feature gating]
Use [`WebSpatialRuntime.supports`](../js-api/WebSpatialRuntime.md) when application behavior depends on whether a specific component, API, event, or option is available. User-Agent parsing describes the runtime environment, but it is not the public capability support contract.
:::

## Exposed On

This property is available on the `navigator` object.

## Mental Model

If the User Agent string matches `/WebSpatial\/(\S+)/`, the current runtime environment has WebSpatial Runtime, and the captured version number is the npm package version of the [React SDK](../../../introduction/getting-started.md#installation) that this runtime fully supports.

If the User Agent string matches `/\sVR\s/`, the current runtime environment supports WebXR.

If the User Agent string matches `\swv\)`, the current Web Runtime is WebView.

If the User Agent string matches `/WSAppShell\/(\S+)/`, the current runtime environment is a [Packaged WebSpatial App](../../../concepts/webspatial-app.md#packaged-webspatial-app) that includes WebSpatial Runtime. If it also matches `"Macintosh"`, the environment is visionOS.

If the User Agent string matches `/PicoWebApp\/(\S+)/`, the current runtime environment is the [Web App Runtime of PICO OS 6](https://developer.picoxr.com/document/web/web-platform/).

## Read / Write Semantics

Read-only.

Example:

```js
const ua = navigator.userAgent.toString();
```

## Fallback Behavior

In environments that do not support WebSpatial, meaning there is no WebSpatial Runtime, the User Agent string does not contain a `WebSpatial` field.

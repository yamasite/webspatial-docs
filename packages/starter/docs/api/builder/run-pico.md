<!--
sidebar_position: 2
description: "Launch a WebSpatial App directly on a PICO OS 6 device for testing, without packaging a native app."
-->

# `webspatial-builder run-pico`

Used to launch a [Web App](../../concepts/webspatial-app.md#web-app) directly on a [PICO OS 6](https://developer.picoxr.com/document/web/web-app/) device over adb. Because PICO OS 6 already includes [WebSpatial Runtime](../../concepts/webspatial-app.md#webspatial-runtime), no native packaging is required — `run-pico` opens the Web App's URL in the PICO Web App runtime.

## Quick Reference

| Item                | Value                                                                  |
| ------------------- | ---------------------------------------------------------------------- |
| Best for            | Iterating on a WebSpatial App on a real PICO OS 6 device.              |
| Requires packaging? | No. PICO OS 6 runs the Web App by URL.                                 |
| Default `--base`    | `http://10.0.2.2:5173/` (the host dev server as seen from the device). |

## When to Use

- When you want to test a WebSpatial App on a physical [PICO OS 6](https://developer.picoxr.com/document/discover/pico-os-6-overview/) device.
- When you do not want to build and install a native app bundle.

## When Not to Use

- When you target a platform without built-in WebSpatial Runtime, such as visionOS — use [`run`](./run.md) for the simulator or [`build`](./build.md) for a test device instead.

## Syntax

**Launch on a PICO OS 6 device**

```bash
webspatial-builder run-pico \
  [--base=<base url>] \
  [--manifest=<local path>] \
  [--manifest-url=<url>]
```

## Option Overview

| Option           | Required | What it controls                                                               |
| ---------------- | -------- | ------------------------------------------------------------------------------ |
| `--base`         | Optional | The base URL hosting the Web App on PICO; defaults to `http://10.0.2.2:5173/`. |
| `--manifest`     | Optional | Points Builder at a local Web App Manifest file.                               |
| `--manifest-url` | Optional | Fetches the manifest directly from a live site URL.                            |

## Options

### `--base`

The base URL that hosts the Web App as reachable from the PICO device. It defaults to `http://10.0.2.2:5173/`, which is the address of the host machine's local dev server as seen from the PICO device. Adjust it if your dev server runs on a different host or port.

### `--manifest`

Tells Builder which path in the current project contains the Web App Manifest file. If no manifest is found, Builder uses a minimal default manifest (`name: "WebSpatialTest"`, `start_url: "/"`).

### `--manifest-url`

Tells Builder to fetch the Web App Manifest directly from the live site by URL.

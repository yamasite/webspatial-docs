---
sidebar_position: 2
description: 'Launch a hosted WebSpatial app on a connected PICO OS 6 device through ADB.'
---

# `webspatial-builder run-pico`

Launches a temporary Web App on the default ADB-connected PICO OS 6 device. It does not package a native app or start a development server.

## Prerequisites

- Install `adb` and make it available on `PATH`.
- Connect exactly the device you want the default ADB target to select.
- Host the Web app at an HTTP or HTTPS URL reachable from the device.

## Syntax

```bash
webspatial-builder run-pico   [--base <url>]   [--manifest <path> | --manifest-url <url>]
```

| Option           | Required | Behavior                                                                                                                  |
| ---------------- | -------- | ------------------------------------------------------------------------------------------------------------------------- |
| `--base`         | No       | Hosted app URL. Defaults to `http://10.0.2.2:5173/`; a trailing slash is added. Query strings and fragments are rejected. |
| `--manifest`     | No       | Manifest path relative to the current directory. Cannot be combined with `--manifest-url`.                                |
| `--manifest-url` | No       | Downloads a remote manifest. Cannot be combined with `--manifest`.                                                        |

Without either manifest option, Builder checks `public/manifest.json`, then `public/manifest.webmanifest`, then uses a development manifest. It replaces `start_url` and `scope` with `--base`, resolves icon URLs against that base, and launches the PICO Web Router with a temporary install type.

Success prints `Launched WebSpatial App on PICO OS 6 from <base>`. An invalid base, invalid manifest icons, conflicting manifest options, missing `adb`, or an ADB launch failure terminates the command with an error.

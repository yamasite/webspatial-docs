---
sidebar_position: 4
description: 'Upload a built Packaged WebSpatial App to App Store Connect for TestFlight or App Store submission.'
---

# `webspatial-builder publish`

Used to submit a [Packaged WebSpatial App](../../concepts/webspatial-app.md#packaged-webspatial-app) to an app store.
It includes packaging and automatically uploads the app to the store after packaging.
For the visionOS App Store, app information must be [filled in beforehand in App Store Connect](../../how-to/app-store-connect.md#submission-to-the-app-store).

## Quick Reference

| Item                       | Value                                                             |
| -------------------------- | ----------------------------------------------------------------- |
| Best for                   | Publishing a Packaged WebSpatial App to an app store.             |
| Required flags             | `--base`, `--u`, `--p`, `--version`, and `--teamId` for visionOS. |
| Does it upload to a store? | Yes, automatically after packaging.                               |

## When to Use {#when-to-use}

- When you want to run a [WebSpatial App](../../concepts/webspatial-app.md) on a [spatial computing](../../concepts/spatial-computing.md) platform that does not include [WebSpatial Runtime](../../concepts/webspatial-app.md#webspatial-runtime), such as visionOS.
- When you want to [test on many real devices or distribute the app publicly](../../introduction/getting-started.md#distribution).

## When Not to Use {#when-not-to-use}

- When you want to run on a [spatial computing](../../concepts/spatial-computing.md) platform that includes [WebSpatial Runtime](../../concepts/webspatial-app.md#webspatial-runtime) and supports running a [WebSpatial App](../../concepts/webspatial-app.md) directly by URL, such as [PICO OS 6](https://developer.picoxr.com/document/web/web-app/).
- When you only need to [preview in a simulator](./run.md) or [preview on a personal test device](./build.md).

## Syntax {#syntax}

```bash title="Package and upload an app-store build" {2-6}
webspatial-builder publish \
  --base=<base url> \
  --u=<username> \
  --p=<password> \
  --version=<app version> \
  --teamId=<id> \
  [--bundle-id=<id>] \
  [--platform=<xr platform>] \
  [--buildType=<build type>] \
  [--export=<local path>] \
  [--manifest=<local path>] \
  [--manifest-url=<url>] \
  [--project=<dist>]
```

## Option Overview

| Option           | Required         | What it controls                                                                   |
| ---------------- | ---------------- | ---------------------------------------------------------------------------------- |
| `--base`         | Yes              | Provides or overrides the base URL used to resolve `start_url`.                    |
| `--u`            | Yes for visionOS | Provides the Apple developer account email used for upload.                        |
| `--p`            | Yes for visionOS | Provides the app-specific password for the Apple developer account.                |
| `--version`      | Yes for visionOS | Sets the app version uploaded to App Store Connect.                                |
| `--teamId`       | Yes for visionOS | Identifies the Apple developer team used to sign the build.                        |
| `--bundle-id`    | Optional         | Selects the registered Apple app identifier.                                       |
| `--platform`     | Optional         | Selects the target XR platform the bundle is built for.                            |
| `--buildType`    | Optional         | Selects the app build type; Builder sets it to `app-store-connect` for publishing. |
| `--export`       | Optional         | Changes the output path of the generated native app bundle.                        |
| `--manifest`     | Optional         | Points Builder at a local Web App Manifest file.                                   |
| `--manifest-url` | Optional         | Fetches the manifest directly from a live site URL.                                |
| `--project`      | Optional         | Points Builder at a custom website build output directory.                         |

## Options {#options}

### `--u`

Required to submit to the visionOS App Store. Comes from the account email in the developer information of the [Apple developer account console](../../how-to/app-store-connect.md#prerequisites).

### `--p`

Required to submit to the visionOS App Store. Comes from the [app-specific password](https://support.apple.com/102654) of the Apple developer account.

### `--teamId`

Required to submit to the visionOS App Store. Comes from the Team ID in the developer information of the [Apple developer account console](../../how-to/app-store-connect.md#prerequisites).

### `--bundle-id`

Optional. Selects the registered Apple app identifier (the Bundle ID from the [Apple developer account console](../../how-to/app-store-connect.md#prerequisites)). The CLI registers it as an optional flag and does not require it to publish.

### `--version`

Required to submit to the visionOS App Store. It must be greater than the version already submitted to App Store Connect.

### `--platform`

Selects the target XR platform the bundle is built for (for example, the platform that provides the matching WebSpatial Runtime). Builder loads the platform module that matches this value.

### `--buildType`

Selects the app build type (for example, the distribution channel). For `publish`, Builder sets this to `app-store-connect` automatically.

### `--export`

By default, the native app bundle generated by WebSpatial Builder is written to the `build/` directory. Use `--export` to change it to a custom path.

### `--base`

Required. Specifies the base URL for the start URL ([`start_url`](https://developer.mozilla.org/en-US/docs/Web/Manifest/start_url)) of the current [Web App](../../concepts/webspatial-app.md#web-app).

If WebSpatial Builder does not know the current Web App's `start_url`, the value of `--base` is used directly as `start_url`.

If the current Web App's `start_url` obtained by WebSpatial Builder is a [relative URL with no domain part](#--manifest), `--base` can provide the domain and complete the URL.

| Resolution state                    | Runtime behavior                                                                                                                                                                                                           |
| ----------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| A complete `start_url` is available | The Packaged App loads HTML/CSS/JS and other Web files from the network at runtime.                                                                                                                                        |
| A complete `start_url` is missing   | WebSpatial Builder packages the [website build output](../../concepts/webspatial-app.md#web-build-tool) into the native app bundle, and the app loads website files offline from local files inside the bundle at runtime. |

If WebSpatial Builder already has the complete `start_url` for the current Web App, `--base` can be used to change the base part of `start_url`.

### `--manifest`

Tells WebSpatial Builder which path in the current project contains the Web App Manifest file. By default it reads from `public/manifest.webmanifest` or `public/manifest.json`.

Fields such as `start_url` in a Web App Manifest are usually relative URLs and do not hardcode the domain or other base URL parts. That means WebSpatial Builder cannot determine how to access the current Web App by URL from the manifest file in the project repository alone. It needs [`--base` to obtain the domain and complete the URL](#--base). Otherwise, the generated Packaged App will contain all [website build output](../../concepts/webspatial-app.md#web-build-tool) and will not load the site from the network.

### `--manifest-url`

Tells WebSpatial Builder to fetch the Web App Manifest directly from the live site by URL.

Because this URL includes the domain and other base parts, even if fields such as `start_url` in the Manifest are relative URLs, WebSpatial Builder can infer and complete `start_url` automatically just like a browser. In this case, WebSpatial Builder effectively embeds the live Web App into the Packaged App by URL, so local project files and parameters such as [`--base`](#--base) are not required.

### `--project`

If WebSpatial Builder cannot determine how to access the current Web App by URL, it packages the [website build output](../../concepts/webspatial-app.md#web-build-tool) from `dist/` into the native app bundle by default.

You can use `--project` to make WebSpatial Builder read the website build output from a different path.

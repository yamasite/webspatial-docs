---
sidebar_position: 0
description: 'Choose the WebSpatial Builder command that matches your preview, packaging, or publishing task.'
---

# WebSpatial Builder commands

`@webspatial/builder` provides the `webspatial-builder` CLI. The published package requires Node.js 24 or later.

| Command                     | Status               | Use it for                                                                   | Main prerequisite                               |
| --------------------------- | -------------------- | ---------------------------------------------------------------------------- | ----------------------------------------------- |
| [`run`](./run.md)           | Public               | Build, install, and launch a packaged app in the visionOS simulator.         | macOS, Xcode, and a visionOS simulator runtime. |
| [`run-pico`](./run-pico.md) | Public               | Open a hosted WebSpatial app on a connected PICO OS 6 device.                | `adb` and one connected device.                 |
| [`build`](./build.md)       | Public               | Produce a signed visionOS archive/export for device testing.                 | macOS, Xcode, Apple team ID, and base URL/path. |
| [`publish`](./publish.md)   | Public               | Build and upload a visionOS app to App Store Connect.                        | Build prerequisites plus upload credentials.    |
| [`launch`](./launch.md)     | Maintenance          | Reinstall and launch a previously prepared test app in a visionOS simulator. | Existing Builder temporary test output.         |
| [`shutdown`](./shutdown.md) | Maintenance          | Shut down the first visionOS simulator selected by Builder.                  | macOS, Xcode, and a visionOS simulator.         |
| `test`                      | Internal placeholder | No user-visible work; the current implementation is empty.                   | Do not use in application workflows.            |

Run `webspatial-builder --help` for the registered command list and `webspatial-builder <command> --help` for Commander-level option requirements.

:::caution[Command names are case-sensitive]
The implemented long options include camel-case names such as `--teamId`, `--buildType`, and `--tryWithoutBuild`. Use the spelling shown in this reference.
:::

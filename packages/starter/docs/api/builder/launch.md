<!--
sidebar_position: 5
description: 'Reinstall and launch a previously prepared Builder test app in a visionOS simulator.'
-->

# `webspatial-builder launch`

A maintenance command that launches a test app from Builder's temporary test output. Prefer [`run`](./run.md) for a complete build-and-launch workflow.

```bash
webspatial-builder launch [--bundle-id <id>]
```

`--bundle-id` defaults to `com.webspatial.test`. The command selects or creates a visionOS simulator, boots it when needed, uninstalls the same bundle ID if present, installs the existing test app, and launches it.

> [!CAUTION] > **Existing temporary output required**
>
> `launch` does not prepare the platform template or build an app. It can fail when the Builder temporary test directory does not contain a compatible app. Run `webspatial-builder run` to create and launch a fresh build.

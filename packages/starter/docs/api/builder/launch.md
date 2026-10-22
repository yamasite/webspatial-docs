<!--
sidebar_position: 5
description: "Launch an already-installed WebSpatial App in the visionOS simulator."
-->

# `webspatial-builder launch`

Used to launch a WebSpatial App that is already installed in the visionOS simulator. Unlike [`run`](./run.md), it does not package, install, or rebuild the app — it only starts the installed app by its bundle identifier.

## Syntax

**Launch the installed app in the simulator**

```bash
webspatial-builder launch [--bundle-id=<bundle id>]
```

## Options

### `--bundle-id`

Selects the app identifier to launch in the simulator. If omitted, Builder uses the default test identifier `com.webspatial.test`.

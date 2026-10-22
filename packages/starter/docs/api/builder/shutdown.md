<!--
sidebar_position: 6
description: "Shut down the visionOS simulator and reserve-command reference for the Builder CLI."
-->

# `webspatial-builder shutdown`

Used to shut down the running visionOS simulator. It is a convenience for cleaning up the simulator between debug sessions; it does not touch any built app bundle.

## Syntax

**Shut down the simulator**

```bash
webspatial-builder shutdown
```

`shutdown` takes no options.

## Reserved command: `test`

The Builder CLI also registers a `test` command:

```bash
webspatial-builder test
```

It is reserved for future use and currently performs no operation.

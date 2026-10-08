---
sidebar_position: 6
description: 'Shut down the visionOS simulator selected by WebSpatial Builder.'
---

# `webspatial-builder shutdown`

```bash
webspatial-builder shutdown
```

Finds the first visionOS simulator selected by Builder and shuts it down with `xcrun simctl`. If it is already shut down, the command reports that state and makes no change.

This command requires macOS, Xcode command-line tools, and an installed visionOS simulator runtime. It does not stop PICO devices or web development servers.

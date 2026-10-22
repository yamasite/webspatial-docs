---
sidebar_position: 3
description: 'Set up the WebSpatial JSX runtime in Rspack or Rsbuild projects through swc-loader configuration.'
---

# How to configure the JSX runtime in Rspack-based projects

React projects based on [Rspack](https://www.rspack.dev/)/[Rsbuild](https://rsbuild.rs/) cannot [configure the JSX Runtime with `jsxImportSource` in `tsconfig`](../introduction/getting-started.md#set-up-your-project). Instead, they need to configure it in `swc-loader`.

:::tip[What to look for]
The key setting is `importSource: "@webspatial/react-sdk"`. That is what redirects JSX compilation to the WebSpatial SDK runtime.
:::

## Rsbuild

```js title="rsbuild.config.ts" {7-9}
import { defineConfig } from "@rsbuild/core";
import { pluginReact } from "@rsbuild/plugin-react";

export default defineConfig({
  plugins: [
    pluginReact({
      swcReactOptions: {
        runtime: "automatic",
        importSource: "@webspatial/react-sdk",
      },
    }),
  ],
});
```

## Rspack

```js title="rspack.config.mjs" {14-15}
export default {
  module: {
    rules: [
      {
        test: /\.tsx?$/,
        exclude: /node_modules/,
        use: {
          loader: "builtin:swc-loader",
          options: {
            jsc: {
              parser: { syntax: "typescript", tsx: true },
              transform: {
                react: {
                  runtime: "automatic",
                  importSource: "@webspatial/react-sdk",
                },
              },
            },
          },
        },
        type: "javascript/auto",
      },
    ],
  },
};
```

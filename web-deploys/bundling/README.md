- root.js
```ts

export { root, __dirname };

// https://stackoverflow.com/questions/46745014/alternative-for-dirname-in-node-when-using-the-experimental-modules-flag/50052194#50052194

import { dirname } from "path";
import { fileURLToPath } from "url";
const __dirname = dirname(fileURLToPath(import.meta.url));
const root = `${__dirname}/..`;


```


```ts

import * as esbuild from "esbuild";
import { __dirname } from "./root.js";
await esbuild
  .build({
    bundle: true,
    format: "cjs",
    logLevel: "error",
    entryPoints: [
      `${__dirname}/server/index.cjs`,
      `${__dirname}/server/lambda.cjs`,
    ], // Ensure these files have .cjs extensions
    outdir: `${__dirname}/dist`, // Outputs to this directory with the same extension as the entry files
    outExtension: { ".js": ".cjs" }, // Ensures output files have .cjs extension
    platform: "node",
    target: ["node20"],
    external: ["vite"],
    sourcemap: true,
  })
  .catch(() => process.exit(1));

```
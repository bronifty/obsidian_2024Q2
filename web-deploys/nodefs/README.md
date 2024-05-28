
```ts
// import { require } from "tsx/cjs/api";

// import * as tsx from "tsx";

  

// const tsLoaded = require("out", import.meta.url);

// const tsFilepath = require.resolve("out", import.meta.url);

  

// const resolvedPath = tsx.require.resolve("out", import.meta.url);

  

// console.log(resolvedPath);

import { readFileSync } from "fs";
const configPath = new URL("./dog.json", import.meta.url);
const config = JSON.parse(readFileSync(configPath));
console.log(config);

```


- simple-rsc 
```ts
/** UTILS */
const appDir = new URL('./app/', import.meta.url);
const buildDir = new URL('./build/', import.meta.url);

function resolveApp(path = '') {
return fileURLToPath(new URL(path, appDir));
}

function resolveBuild(path = '') {
return fileURLToPath(new URL(path, buildDir));
}

const reactComponentRegex = /\.jsx$/;

```

- simon wardley pioneer town settler city planner
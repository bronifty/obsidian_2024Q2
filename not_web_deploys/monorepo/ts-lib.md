
https://dev.to/vinomanick/create-a-monorepo-using-pnpm-workspace-1ebn
https://dev.to/vinomanick/create-a-typescript-utility-library-using-vite-916


```sh
corepack enable
corepack prepare pnpm@latest --activate
```

```sh
pnpm create vite <app-name> --template vanilla-ts
  cd <app-name>
  pnpm install && pnpm update
```

```sh
  git init
  npm pkg set engines.node=">=22.2.0" // Use the same node version you installed
  echo "#Typescript utility library" > README.md
```

- get path resolution and automatic typescript types from the build
```sh
pnpm add -D @types/node 
pnpm add -D vite-plugin-dts
```

```sh
touch vite.config.ts
```

```ts
import { defineConfig } from 'vite'
import { resolve } from 'path'
import dts from 'vite-plugin-dts'

// https://vitejs.dev/config/
export default defineConfig({
  build: { lib: { entry: resolve(__dirname, 'src/main.ts'), formats: ['es'] } },
  resolve: { alias: { src: resolve('src/') } },
  plugins: [dts()],
})
```
- update the library's package.json to add the entry file and types
	- the "main" file in dist will be automatically created by the build and based on the package name
```json
// package.json
{
"name": "marcs-observable",
"main": "./dist/marcs-observable.js",
"types": "./dist/main.d.ts",
}
```


```
- update main.ts (your ts library file)
```ts

// main.ts
export const add = (a: number, b: number) => a + b
export const sub = (a: number, b: number) => a - b
```
- remove sample files
```sh
rm -rf src/style.css src/counter.ts
```

- test locally by running vite (it will spin up a dev server and serve the local html file)
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/vite.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Vite + TS</title>
  </head>
  <body>
    <script type="module" src="/src/main.ts"></script>
    <script type="module">
      import { add, sub } from './src/main'

      const addOutput = document.getElementById('addOutput')
      const subOutput = document.getElementById('subOutput')
      const input1 = document.getElementById('input1')
      const input2 = document.getElementById('input2')
      const getNumber = (element) => parseInt(element?.value)
      const eventHandler = () => {
        const firstVal = getNumber(input1)
        const secondVal = getNumber(input2)
        if (addOutput && subOutput) {
          addOutput.innerText = add(firstVal, secondVal).toString()
          subOutput.innerText = sub(firstVal, secondVal).toString()
        }
      }
      eventHandler()
      input1?.addEventListener('change', eventHandler)
      input2?.addEventListener('change', eventHandler)
    </script>

    <input id="input1" type="number" value="4" />
    <input id="input2" type="number" value="3" />
    <p>Sum of two numbers = <span id="addOutput"></span></p>
    <p>Diff of two numbers = <span id="subOutput"></span></p>
  </body>
</html>
```



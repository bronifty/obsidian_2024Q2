
https://vike.dev/
https://vitejs.dev/guide/ssr
https://deploy-preview-16089--vite-docs-main.netlify.app/guide/
https://github.com/vitejs/vite/discussions/16358
https://github.com/bronifty-aws/vite-ssr-react

# vite-ssr-react

https://deploy-preview-16089--vite-docs-main.netlify.app/guide/

  

# Vite OSS Repo

  

```bash

git clone https://github.com/vitejs/vite.git

cd vite

pnpm install

cd packages/vite

pnpm run build

pnpm link --global # globally link the vite package you built

```

  

# Vite Project Using Vite OSS Repo

  

- in another window

```bash

pnpm create vite # choose others react ssr

cd react-ssr # cd into your react ssr project

pnpm link --global vite # link the app project to your global vite

```

- in the newly created project, add the local vite to dev deps

```json

"devDependencies": {

"vite": "file:/Users/bronifty/codes/oss/vite"

}

```

- https://deploy-preview-16089--vite-docs-main.netlify.app/guide/assets.html
```ts
import imgUrl from './img.png'
document.getElementById('hero-img').src = imgUrl

import imgUrl from './img.svg'
document.getElementById('hero-img').style.background = `url("${imgUrl}")`

// does not work with SSR apparently because import.meta.url is not available in node??
const imgUrl = new URL('./img.png', import.meta.url).href
document.getElementById('hero-img').src = imgUrl
```

https://github.com/Menci/vite-plugin-wasm


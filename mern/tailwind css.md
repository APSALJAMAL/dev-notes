# tailwind css


```js
npm create vite@latest my-project
cd my-project
```

```js
npm install tailwindcss @tailwindcss/vite
```
### vite.config.ts
```ts
import { defineConfig } from 'vite'
import tailwindcss from '@tailwindcss/vite'
export default defineConfig({
  plugins: [
    tailwindcss(),
  ],
})
```

### index.css
```css
@import "tailwindcss";
```
```js
npm run dev
```
### index.html
```html
<!doctype html>
<html>
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <link href="/src/style.css" rel="stylesheet">
</head>
<body>
  <h1 class="text-3xl font-bold underline">
    Hello world!
  </h1>
</body>
</html>
```
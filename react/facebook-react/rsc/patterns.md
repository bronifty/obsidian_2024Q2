
# ben holmes simple-rsc
- api server with html and rsc routes
- client script to create root from html template and inject content through a stream requested by createFromFetch 
	- (Fetch your server component stream from `/rsc` and render results into the root element as they come in.)

server.js
```typescript
app.get('/', async (c) => {
	return c.html(`
	<!DOCTYPE html>
	<html>
	<head>
		<title>React Server Components from Scratch</title>
		<script src="https://cdn.tailwindcss.com"></script>
	</head>
	<body>
		<div id="root"></div>
		<script type="module" src="/build/_client.js"></script>
	</body>
	</html>
	`);
});

app.get('/rsc', async (c) => {
	// Note This will raise a type error until you build with `npm run dev`
	const Page = await import('./build/page.js');
	// @ts-expect-error `Type '() => Promise<any>' is not assignable to type 'FunctionComponent<{}>'`
	const Comp = createElement(Page.default);

	const stream = ReactServerDom.renderToReadableStream(Comp, clientComponentMap);
	return new Response(stream);
});
```

client.js
```typescript
// @ts-expect-error `root` might be null
const root = createRoot(document.getElementById('root'));

/**
 * Fetch your server component stream from `/rsc`
 * and render results into the root element as they come in.
 */
createFromFetch(fetch('/rsc')).then((comp) => {
root.render(comp);
});
```


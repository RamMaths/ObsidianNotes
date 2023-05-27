
We import `BrowserRouter`, `Routes` y `Route` from `react-router-dom` for our initial setup.

- `BrowserRouter`: Is going to connect to the actual browser.
- `Routes`: Is going to be the parent for all our routes
- `Route`: We'll use this to setup a single page.

	When working with `Route` the *path* property it's gonna start in the root if we only type */* (our homepage). For the *element* property we just need to setup what we want to see on the screen
 
```jsx
<Route path="/" element={<div>Home page</div>}/>
<Route 
	path="testing" 
	element={
		<div>
			<h2>Testing</h2>
		</div>
	}
/>
```

We can also set components in the element property

```js
<Routes>
	<Route path='/' element={<Home />}/>
<Routes/>
```
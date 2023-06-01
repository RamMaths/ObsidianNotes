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

```jsx
<BrowserRouter>
  <Routes>
	<Route path='/' element={<Home />}/>
	<Route path='about' element={<About />}/>
	<Route path='products' element={<Products />}/>
  </Routes>
</BrowserRouter>
```

If we want to navigate internally in our project we will grab the `Link` component from the `react-router-dom` module. If we want to navigate externally for example [google.com](google.com), you still need to use the `Link` component. 

The `Link` component is looking for one thing, the `to=` prop. If for example we wanted to go to the about page we would do:

```jsx
<Link to='/about'>
	About
</Link>
```

The links need to match with the value that we set in the `path=` property using the `Route` component.

When we go to inexistent rout React will basically display nothing. That's why we need to setup an Error page in order to handle this scenario. 

```jsx
<Route path='*' element={<Error/>} />
```

It's important to have in mind that every component that it's not a child of the `Routes` component it's going to appear in every single page that we go, this is great for the Navbar and Footer.

```jsx
    <BrowserRouter>
	  <Navbar />
      <Routes>
        <Route path='/' element={<Home />}/>
        <Route path='about' element={<About />}/>
        <Route path='products' element={<Products />}/>
        <Route path='*' element={<Error />}/>
      </Routes>
	  <Footer />
    </BrowserRouter>
```

If we wanted to nest some routes inside of another rout, we just need to set up the `path` property of the `Routes` component to the route that we want to be the parent and the nest  the routes that we are looking for, this routes are going to be next to their parent route in the url.

```jsx
<Route path='/' element={<Home />}>
  <Route path='about' element={<About />}/>
  <Route path='products' element={<Products />}/>
  <Route path='*' element={<Error />}/>
</Route>
```

If we leave it like this you'll see only the parent element even if you are changing the routes. In order to solve this we need to go to the parent and import `Outlet` and whatever we'll setup around this `Outlet` component is going to be the shared layout across the pages that are nested inside of the parent. Now that we know this we can set up a Navbar to share across the nested pages to Home. 

```jsx
const Home = () => {
  return (
    <>
      <Navbar />
      <Outlet />
    </>
  );
};
```

The problem with this is that whatever we put in the home page even its content it's going to be shared across its children components. The solution is the use of `index`
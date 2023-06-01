
# Undefined routes

In order to create a handle function for all the routes that are not cached. Since we want to catch not only the `get` method but the rest of the http methods, we need a way to handle all of these with one single move because otherwise we would be creating a handle function for each method and it's what we want to avoid.  To solve this we can use the `all` method like the following

```js
app.use('*', (req, res, next) => {
  res.status(404).json({
    status: "fail",
    message: `Can't find the ${req.originalUrl}`
  });
});
```

# Error Handling

## Type of errors

|Operational Errors|Programming Errors|||
|:--:|:--:|
|Problems that we can predict will happen at some point, so we just need to handle them in advance.|Bugs that we developers introduce into our code. Difficult to find and handle|
| - Invalid path accessed<br>- Invalid user input<br>- Failed to connect to server<br>- Failed to connect to database<br>- Request timeout|- Reading properties on `undefined`<br>- Passing a number where an object is expected<br>- Using `await` without `async` |

When we're talking about error handling with Express, we mainly just mean operational errors because these are the ones that are easy to catch and to handle with our express application.

Express actually comes with error handling out of the box. So all we have to do is to write a global express error handling middleware which will then catch all the errors coming from all over the application.

The beauty of having a global error handling middleware is that it allows for a nice separation of concerns. So we don't have to worry about error handling right in our business logic or our controllers or really anywhere in our application. We can simply send the errors down to the error handler which will then decide  what to do with the next.

# Implementing the global error handler

<mark>Passing four parameters express automatically knows that this entire function is an error handling middleware.</mark>

```node
app.use((err, req, res, next) => {
  //
  next();
});
```

When talking about middleware in express <mark>when the `next` function receives an argument</mark>, no matter what it is, <mark>Express will assume that whatever we passed into next is gonna be an error</mark>,  and then it will skip all the other middlewares in the middleware stack.

That's why we can do 

```node
//undefined routes
app.all('*', (req, res, next) => {
  const err = new Error(`Can't find the ${req.originalUrl}`);
  err.status = 'failed';
  err.statusCode = 404;

  next(err);
});
```

```node
//error handling
app.use((err, req, res, next) => {
  //500 means internal server error and it's usually the standard
  err.statusCode = err.statusCode || 500;
  //500 means error and a 400 means a fail
  err.status = err.status || 'error';
  res.status(err.statusCode).json({
    status: err.status,
    message: err.message
  });
});
```

# Better Error Classes

It's better to have a dedicated class to define our own errors

```js
class AppError extends Error {
  constructor(message, statusCode) {
    super(message);

    this.statusCode = statusCode;
    this.status = `${statusCode}`.startsWith('4') ? 'failed' : 'error';
    this.isOperational = true;

    Error.captureStackTrace(this, this.constructor);
  }
}
```

`Error.captureStackTrace(this, this.constructor)`: This line captures the stack trace for the `AppError` instance. It sets the stack trace property of the instance by calling `captureStackTrace` on the `Error` object and passing the instance and its constructor.

All the errors have a stack trace, which is represented by the `stack` property name which shows us where the error happened.

```js
app.use((err, req, res, next) => {
  console.log(err.stack);
});
```

The middleware that we have to handle our errors it's great but the word *handle* also means *controller* if we follow the MVC architecture that's why we need to separate it from where we mount the routes.
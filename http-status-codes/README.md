# ![Express Error Handling - HTTP Status Codes](./assets/hero.png)

**Learning Objective:** By the end of this lesson, students will understand how to use HTTP status codes for effective error handling.

## The role of HTTP status codes in error handling

HTTP status codes play an important role in error handling by improving the developer's debugging experience. Using the appropriate status codes with error responses can help developers identify the cause of an issue.

For example:

- **400 Series**: Suggests a problem with the client's request.
- **500 Series**: Suggests a problem on the server.

Common HTTP status codes include:

- `400`: Bad Request
- `401`: Unauthorized
- `403`: Forbidden
- `404`: Not Found
- `500`: Internal Server Error

A comprehensive list of HTTP status codes can be found [here](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status).

## Logging Status Codes with Morgan

During development, logging the status codes of requests can be helpful, especially in regards to debugging. 

In Express, we can use logging middleware like [morgan](https://expressjs.com/en/resources/middleware/morgan.html). The `morgan` package provides detailed logs on requests, including their status codes. 

To use `morgan` in your application, run the following command in your terminal:

```bash
npm i morgan
```

Next, import the package in `server.js`:

```javascript
// server.js
const morgan = require('morgan');
```

Finally, add it to the middleware pipeline:

```javascript
// server.js
app.use(morgan('dev'));
```

## Using status codes in routes

We can add a status code to a response by making a small modification to the `res` object:

```javascript
res.status(500).render('index.ejs');
```

In the code above, we are chaining an additional `status()` method onto the `res` object. The `status()` method allows us to set the HTTP status code of the response. Note that this method does not impact the view that we render.

Let's see how this tecnique can be applied to our wildcard route:

```javascript
// server.js
app.get('*', function (req, res) {
  res.status(404).render('error.ejs', {
    msg: 'Route not found!',
  });
});
```

In this route, we set the status to `404` (Not Found). If we navigate to an undefined path in the browser, `morgan` will log a message like the following:

```plaintext
GET /not-a-path 404 26.648 ms - 341
```

This log entry shows the HTTP method (`GET`), the requested path (`/not-a-path`), and the response status code (`404`). It also displays the time taken to process the request (`26.648 ms`), and the size of the response (`341`).
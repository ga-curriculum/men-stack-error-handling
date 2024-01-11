# ![Express Error Handling - Error Pages](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to implement error pages in a web application.

## What is an error page?

An error page is just a web page that is used to display error messages to an application's users. These pages inform users that an error along with some context on what caused the error.

## Building an error page

Let's demonstrate how an error page can be added to an application. We'll adhere to the DRY (Don't Repeat Yourself) principle and create a page that can be used to display a variety of errors.

Run the following in your terminal:

```bash
touch views/error.ejs
```

Next, add the following to your new view:

```html
<!-- views/error.ejs -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Error Page</title>
</head>
<body>
  <h1>Error</h1>
  <p><%= msg %></p>
  <button onclick="window.history.back()">Go Back</button>
</body>
</html>
```

This code sets up a basic error page template. The `<%= msg %>` will display whatever error message has been passed to the view. This allows us to rely on the same template for any error messages we wish to relay to our users. 

You'll also notice the inclusion of a back button. This button makes use of `window.history`. [The `window.history` property](https://developer.mozilla.org/en-US/docs/Web/API/Window/history) holds methods for manipulating the browser history. The `back()` method will navigate the user to the previous page.

To make use this error page in your application, you can update the `catch` block of an existing route handler like so:

```javascript
// controllers/fruits.js
router.post('/fruits', async (req, res) => {
  try {
    if (!req.body.name.trim()) {
      throw new Error('Invalid input: The name field cannot be empty!');
    }
    await Fruit.create(req.body);
    res.redirect('/fruits');
  } catch (err) {
    res.render('error.ejs', { msg: err.message });
  }
});
```

## Handling undefined routes

At the moment, our error page is only rendered when an issue occurs within a route handler. What about `404` errors caused by users navigating to nonexistent pages? This can happen when a user types a URL directly into the address bar. In that scenario, no route would respond to the user's request.

We can handle a scenario like that with a catch-all route handler:

```javascript
// server.js
app.get('*', function (req, res) {
  res.render('error.ejs', { msg: 'Page not found!' });
});
```

When this route handler is placed after all predefined routes, it acts as a safeguard for undefined paths. The `*` path acts as a wildcard. It matches any route that hasn't been matched by previous route handlers. If a user tries to access a URL that doesn't correspond to any routes in your application, the handler will be called upon and the error page will be rendered with a "Page not found!" message.
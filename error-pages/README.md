# ![MEN Stack Error Handling - Error Pages](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to implement error pages in a web application.

## What is an error page?

An error page is just a web page that is used to display error messages to an application's users. These pages inform users of an error, along with some context on what caused the error.

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
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
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

To make use of this error page in your application, you can update the `catch` block of an existing controller like so:

```javascript
// controllers/fruits.js
router.post("/", async (req, res) => {
  try {
    if (!req.body.name.trim()) {
      throw new Error("Invalid input: The name field cannot be empty!");
    }
    await Fruit.create(req.body);
    res.redirect("/fruits");
  } catch (err) {
    res.render("error.ejs", { msg: err.message });
  }
});
```

To see it in action, try to create a fruit with a missing name or a name that is all spaces.

## Handling undefined routes

At the moment, our error page is only rendered when an issue occurs within a route handler/controller. What about `404` errors caused by users navigating to nonexistent pages? This can happen when a user types a URL directly into the address bar. In that scenario, no route would respond to the user's request.

We can handle a scenario like that with a catch-all route handler:

<bold>Make sure you put this after all other declared routes in your `server.js`: we don't want it to interfere with a declared route!</bold>

```javascript
// server.js
app.get("*", function (req, res) {
  res.render("error.ejs", { msg: "Page not found!" });
});
```

When this route handler is placed **_after all predefined routes_**, it acts as a safeguard for undefined paths. The `*` path acts as a wildcard. It matches any route that hasn't been matched by previous route handlers. If a user tries to access a URL that doesn't correspond to any routes in your application, the handler will be called upon and the error page will be rendered with a "Page not found!" message.

It's very important that this is the last route in your file. If you were to, for example, put your `/fruits` index route after this route, you would never be able to get to the `index` page. The 404 page would instead be displayed because it will handle any `GET` route before it's able to reach the `index` route. This is similar to how `/fruits/:fruitId` will handle `/fruits/new` if `/fruits/new` comes after `/fruits/:fruitId`. Try it!

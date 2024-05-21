# ![MEN Stack Error Handling - Flash Messages](./assets/hero.png)

**Learning Objective:** By the end of this lesson, students will understand how to implement flash messages in Express applications.

## Understanding flash messages

Flash messages are a way of displaying temporary messages to uses in web applications. Typically, they show a user immediate feedback about the result of an action the user has taken. For example, after a user submits a form, there might be a flash message that appears indicating the resource was saved successfully. Flash messages can also be used to notify the user of an error with a given operation.

They are particularly useful for delvering messages across redirects. If a resource is deleted, and the user is redirected to another page, a flash message can be used to notify them of the operation's outcome on the new page.

Flash messages are often implemented using _session storage_. This means the message is stored in the user's session and is cleared once it has been displayed.

> 📚 [_Session storage_](https://expressjs.com/en/resources/middleware/session.html) in Express is a way of storing data on the server for users, keeping track of their activity on the website. They're very similar to cookies.

## Implementing flash messages with custom middleware

Let's build out some custom middleware to assist us in using flash messages.

Add the following to the end of the middleware pipeline in `server.js`:

```javascript
// server.js
app.use((req, res, next) => {
  if (req.session.message) {
    res.locals.message = req.session.message;
    req.session.message = null;
  }
  next();
});
```

Our middleware checks if there is a message stored in `req.session.message`. If it finds one, it transfers this message to `res.locals.message`. The `res.locals` object is accessible in our views, making it easy to display the flash message on the appropriate page.

After updating `res.locals`, we set `req.session.message` to `null`. This ensures that the flash message is only displayed once for the user. Without this, the message would persist in the session and could be displayed at an inappropriate time. By accomplishing this in our middleware, we save ourselves the step of clearing our the session in every controller function that needs to display a message.

## Displaying flash messages

To display an actual message, we'll need to modify one of our controllers.

Update the `post` controller as shown below:

```javascript
// controllers/fruits.js
router.post("/", async (req, res) => {
  try {
    await Fruit.create(req.body);
    req.session.message = "Fruit successfully created.";
    res.redirect("/fruits");
  } catch (err) {
    req.session.message = err.message;
    res.redirect("/fruits");
  }
});
```

In the code above, we are adding one of two messages to `req.session.message`, depending on the outcome of the `create()` operation. In either case, once the message is added to `req.session.message`, a `redirect()` occurs. The `redirect()` causes the browser to make a new request to `/fruits`. When this new request comes in, our custom middleware is triggered, at which point at which point the message is added to `res.locals.message`, and cleared from the session.

For users to see the flash message, we'll need to modify the view that corresponds with the path users are being redirected to upon creation (`'/fruits'` in this case).

Add the following to the view:

```html
// views/fruits/index.ejs <% if (typeof message !== 'undefined') { %>
<p><%= message %></p>
<% } %>
```

> 🏆 By using `typeof message !== 'undefined'`, we check if message variable exists before trying to use it. This is a safer way to check for the presence of a variable in EJS because it doesn't throw an error if the variable is not defined. This accounts for situations where message is not passed to the template.

If we try creating a fruit, we should see the following text at the top of the index view:

```plaintext
Fruit successfully created.
```

To view our error message, we can temporarily modify the controller to simulate an error:

```javascript
// controllers/fruits.js
router.post("/", async (req, res) => {
  try {
    throw new Error("A problem occured!");
    await Fruit.create(req.body);
    req.session.message = "Fruit successfully created.";
    res.redirect("/fruits");
  } catch (err) {
    req.session.message = err.message;
    res.redirect("/fruits");
  }
});
```

Try to create a new fruit, using the browser.

> 🚨 Remember to remove the simulated error after testing!

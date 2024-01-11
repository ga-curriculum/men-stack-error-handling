# ![Express Error Handling - Server-Side Error Handling](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will understand the role of server-side error handling.

## What is server-side error handling?

Server-side error handling refers to the process of managing errors that occur within the server of an application. The server is responsible for a variety of operations, and robust server-side error handling is crucial for the stability and security of any application.

Below are a few benefits of effective server-side error handling:

- **Stability**: Server-side error handling helps maintain the stability of an application by preventing crashes when the unexpected happens.

- **Data Integrity**: Server-side error handling can be helpful in maintaining consistent records in our database. For example, if a user tries to submit an invalid email, the server can detect this and prevent the faulty data from being stored, while also alerting the user to correct their input.

- **Security**: Server-side error handling is also important for authentication and authorization. It helps us detect when a user attempts to access resources they shouldn't, and provide the appropriate responses while keeping the application secure.

## Error handling with `try...catch`

One of the essential mechanisms for error handling on the server is the `try...catch` block. When using a `try...catch` block, the program first attempts to execute code written inside the `try`. If an error occurs, control is passed to the `catch` block where we can place code that handles and resolves the error.

In the context of server-side error handling, `try...catch` blocks are quite versatile and can help manage a range of issues including:

- **Database errors**: A `try...catch` block can handle errors that occur during database operations, like querying the database using a Mongoose Model.

- **Validation errors**: A `try...catch` block can handle errors thrown by Mongoose when data submitted by a user does not conform to the shape of the schema.

- **Reference errors**: A `try...catch` block can handle reference errors, such as accessing an undefined property of an object.

- **Asynchronous operations**: When used within an asynchronous function, `try...catch` blocks can manage errors arising from asynchronous operations.

### Practical example

Let's see a practical example of how a `try...catch` block could improve our application.

Take a look at our `fruitSchema` in `models/fruit.js`:

```javascript
// models/fruit.js
const fruitSchema = mongoose.Schema({
  name: {
    type: String,
    required: true,
  },
});
```

Notice how the `name` property is `required`. This prevents empty `name` strings from being added to the database by triggering a validation error if the condition is not met.

Let's update our controller function for creating fruits as shown below:

```javascript
// controllers/fruits.js
router.post('/fruits', async (req, res) => {
  try {
    await Fruit.create(req.body);
    res.redirect('/fruits');
  } catch (err) {
    console.log(err.message) // Logs the error message
  }
});
```

Now, when a user submits a form without filling out the `name` field, the resulting validation error can be dealt with in our `try...catch` block. Because the operation in the `try` has failed, control is passed to the `catch` block, where it receives an `Error` object as an argument. The `Error` object will contain a `message` property with details about the error that occurred.

If the `name` field is ever missing from the form data, the terminal would display the following message:

```plaintext
Fruit validation failed: name: Path `name` is required.
```

However, it's important to note that `try...catch` blocks aren't perfect. There are many issues that will go undetected if a `try...catch` is the only safeguard applied. This leads us to the need for custom error handling.

## Custom error handling

With custom error handling, we can address more complex or specific error conditions that the standard `try...catch` might miss. Custom errors also allow us to write our own error messages that improve the user experience and aid in the process of debugging.

Custom error handling can be useful when:

- User input does not meet certain criteria.
- A resource cannot be found.
- A user's action violates certain rules.
- Unexpected conditions arrise in data.

### Practical example

Let's see an example of custom error handling at work.

Imagine a scenario where a user fills out a form, but accidentally submits it with a `name` field that contains only blank spaces. While technically not empty, we should consider this invalid input. However, this kind of input does not trigger a validation error based on our current schema, which only checks for the existence of a value, not its content.

Here is how we could modify our code to handle this edge case:

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
    res.render('fruits/new.ejs', { errorMessage: err.message });
  }
});
```

Notice the `if` statement in the example above. 

The [trim() method](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/trim) returns a new string with the white space removed. We've applied the method to the `name` property of `req.body`. If the `name` field is invalid, we create and `throw` a new error with a custom error message.

This action passes control to our `catch` block, where the error message is passed to our view. As a result, the `create()` method is never called upon, and the invalid `name` input is never added to the database.

Finally, we can display the `errorMessage` for users by adding the following to our view:

```html
<!-- views/fruits/new.ejs -->
<% if (typeof errorMessage !== 'undefined') { %>
  <p><%= errorMessage %></p>
<% } %>
```
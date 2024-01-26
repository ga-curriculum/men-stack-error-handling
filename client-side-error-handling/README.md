# ![MEN Stack Error Handling - Client-Side Error Handling](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will understand the benefits of handling errors in the client-side code.

## What is client-side error handling?

Client-side error handling involves more than just displaying error message sent from the server. It allows us to preemptively address potential errors within the browser. This approach can be achieved through techniques like form validation, visual feedback, and client-side scripts.

## HTML form validation

HTML form validation is an effective method for preempting issues on the client side. 

HTML form validation involves checking user input against specific criteria before the form is submitted to the server.  These criteria often include things like required fields, data formats, and limits on character counts. With HTML form validation, we can prevent invalid form data from reaching the server, while also improving user experience with immediate feedback.

Let's see an example of HTML form validation using the `required` attribute.

Update the `<input>` in `views/fruits/new.ejs` with a `required` attribute:

```html
<!-- views/fruits/new.ejs -->
<form action="/fruits" method="POST">
  <input 
    id="name"
    type="text" 
    name="name" 
    required
  >
  <button type="submit">Submit</button>
</form>
```

Now the `required` attribute on the `<input>` element will prevent the `<form>` from being submitted if the field is empty. The browser will also automatically generate and display an error message notifying the user of the issue. 

> 💡 Note, these messages are handled by the browser and can vary in appearance depending on the provider.

## CSS feedback

CSS can be used in tandem with HTML form validation by providing visual feedback for errors.

For example, you can change the border color of input fields to red when there is a validation error. This visual feedback enhances user experience by making it clear which fields need correction.

Take a look at the following CSS rule:

```css
input:invalid {
  border: 1px solid red;
}
```

This rule would add a red border to invalid `<input>` fields, indicating which fields need attention.

Let's apply it to our fruit form with an internal stylesheet:

```html
<!-- views/fruits/new.ejs -->
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Create a Fruit</title>
  <style>
    input:invalid {
      border: 1px solid red;
    }
  </style>
</head>
```

Try it out!

## Pitfalls of client-side error handling

While client-side error handling improves user experience, it's important to be aware of its limitations:

- **Security Risks**: Bad actors can manipulate client-side code using browser tools, bypassing the validations you include. Therefore, it's best not to rely solely on client-side validation for sensitive data.
  
- **Inconsistent Appearance**: Different browsers handle client-side validations differently, leading to inconsistent user experiences.

- **Limited Control**: Some errors, like issues stemming from database operations, cannot be handled client-side.

While client-side error handling is useful for enhancing user experience and reducing server load, it should be used in a complementary fashion to robust server-side error handling.
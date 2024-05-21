# ![MEN Stack Error Handling - Setup](./assets/hero.png)

## Setup

Open your terminal and navigate to your `lectures` directory:

```bash
cd ~/code/ga/lectures
```

## Cloning the Auth boilerplate

This lecture uses the [`MEN Stack Auth Template`](https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template) as starter code. Doing so allows us to have a connection established to our MongoDB Atlas, add functioning auth for our user model, and install some of the packages we will need for this lesson.

Navigate to the [MEN Stack Auth Template](https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template) and clone the repository to your machine:

```bash
git clone git@git.generalassemb.ly:modular-curriculum-all-courses/men-stack-session-auth-template.git
```

Once we have the repository on our machines, we can change the name of the directory to better reflect this lesson: `men-stack-error-handling`

```bash
mv men-stack-session-auth-template men-stack-error-handling
```

Next, `cd` into your renamed directory:

```bash
cd men-stack-error-handling
```

Install the dev dependencies:

```bash
npm i
```

Create a `.env` file:

```bash
touch .env
```

Open the project in VS Code

```bash
code .
```

Add the following to your `.env`:
In the .env file:

```plaintext
SESSION_SECRET=secret-string-unique-to-your-app
MONGODB_URI=your-mongo-db-ur-goes-here
```

> 🚨 Be sure to update the `MONGODB_URI` accordingly!

Create a `.gitignore` file in your project's root directory:

```bash
touch .gitignore
```

Add the following to your `.gitignore` file:

```plaintext
.env
```

## Scaffolding the application

Before we get started, let's add the scaffolding for a basic application.

### Creating a model

First we'll need a model.

Run the following command in your terminal:

```bash
touch models/fruit.js
```

Add the following to `models/fruit.js`:

```javascript
// models/fruit.js
const mongoose = require("mongoose");

const fruitSchema = mongoose.Schema({
  name: {
    type: String,
    required: true,
  },
});

const Fruit = mongoose.model("Fruit", fruitSchema);

module.exports = Fruit;
```

### Adding controllers

Next, we'll add some standard controller functions.

Run the following command:

```bash
touch controllers/fruits.js
```

Add the following to `controllers/fruits.js`:

```javascript
// controllers/fruits.js
const express = require("express");
const router = express.Router();

const Fruit = require("../models/fruit.js");

router.get("/new", (req, res) => {
  res.render("fruits/new.ejs");
});

router.post("/", async (req, res) => {
  await Fruit.create(req.body);
  res.redirect("/fruits");
});

router.get("/", async (req, res) => {
  const foundFruits = await Fruit.find();
  res.render("fruits/index.ejs", { fruits: foundFruits });
});

module.exports = router;
```

### Adding views

And finally, we'll include views for creating and viewing our fruits.

Run the following command:

```bash
mkdir views/fruits
```

Run the following command:

```bash
touch views/fruits/index.ejs
```

Add the following to `views/fruits/index.ejs`:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Fruits Index</title>
  </head>
  <body>
    <a href="/fruits/new">Create a fruit</a>
    <h1>Here Are All the Fruits</h1>
    <ul>
      <% fruits.forEach((fruit)=> { %>
      <li><%= fruit.name %></li>
      <% }) %>
    </ul>
  </body>
</html>
```

Run the following command:

```bash
touch views/fruits/new.ejs
```

Add the following to `views/fruits/new.ejs`:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Create a Fruit</title>
  </head>
  <body>
    <h1>Create a New Fruit!</h1>
    <form action="/fruits" method="POST">
      <label for="name">Name:</label>
      <input type="text" name="name" id="name" />
      <button type="submit">Add fruit</button>
    </form>
    <a href="/fruits">Back to fruits index</a>
  </body>
</html>
```

Let's now update our `server.js` to import the fruits controller and use it for our `/fruits` routes. Make sure your `server.js` looks exactly like this:

```javascript
const dotenv = require("dotenv");
dotenv.config();
const express = require("express");
const app = express();
const mongoose = require("mongoose");
const morgan = require("morgan");
const session = require("express-session");

const authController = require("./controllers/auth.js");
const fruitsController = require("./controllers/fruits.js"); // add this

const port = process.env.PORT ? process.env.PORT : "3000";

mongoose.connect(process.env.MONGODB_URI);

mongoose.connection.on("connected", () => {
  console.log(`Connected to MongoDB ${mongoose.connection.name}.`);
});

app.use(express.urlencoded({ extended: false }));
app.use(
  session({
    secret: process.env.SESSION_SECRET,
    resave: false,
    saveUninitialized: true,
  })
);

app.get("/", (req, res) => {
  res.render("index.ejs", {
    user: req.session.user,
  });
});

app.get("/vip-lounge", (req, res) => {
  if (req.session.user) {
    res.send(`Welcome to the party ${req.session.user.username}.`);
  } else {
    res.send("Sorry, no guests allowed.");
  }
});

app.use("/auth", authController);
app.use("/fruits", fruitsController); // add this

app.listen(port, () => {
  console.log(`The express app is ready on port ${port}!`);
});
```

Also, we will need to update our landing page to add some links to our fruits pages. Update the `views/index.ejs` to this:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Home Page</title>
  </head>
  <body>
    <% if (user) { %>
    <h1>Welcome to the app, <%= user.username %>!</h1>
    <p>
      <a href="/auth/sign-out">Sign out</a>
    </p>
    <% } else { %>
    <h1>Welcome to the app, guest.</h1>
    <p>
      <a href="/auth/sign-up">Sign up</a> or
      <a href="/auth/sign-in">Sign in</a>.
    </p>
    <% } %>
    <p><a href="/fruits">View the fruit menu</a></p>
    <p><a href="/vip-lounge">Get into the VIP Users Only lounge!</a></p>
  </body>
</html>
```

# ![MEN Stack Error Handling - Setup](./assets/hero.png)

## Setup

Open your terminal and navigate to your `lectures` directory:

```bash
cd ~/code/ga/lectures
```

Fork and clone the repository:

```bash
git clone https://git.generalassemb.ly/modular-curriculum-all-courses/men-stack-session-auth-template.git
```

Install the dev dependencies:

```bash
npm i
```

Create a `.env` file:

```bash
touch .env
```

Add the following to your `.env`:
In the .env file:

```plaintext
SESSION_SECRET=secret-string-unique-to-your-app
MONGODB_URI=mongodb+srv://<username>:<password>@sei-w0kys.azure.mongodb.net/error-handling?retryWrites=true
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
const mongoose = require('mongoose');

const fruitSchema = mongoose.Schema({
  name: {
    type: String,
    required: true,
  },
});

const Fruit = mongoose.model('Fruit', fruitSchema);

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
const express = require('express');
const router = express.Router();

const Fruit = require('../models/fruit.js');

router.get('/fruits/new', (req, res) => {
  res.render('fruits/new.ejs');
});

router.post('/fruits', async (req, res) => {
  await Fruit.create(req.body);
  res.redirect('/fruits');
});

router.get('/fruits', async (req, res) => {
  const foundFruits = await Fruit.find();
  res.render('fruits/index.ejs', { fruits: foundFruits });
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
        <input type="text" name="name" id="name" required>
        <button type="submit">Add fruit</button>
      </form>
      <a href="/fruits">Back to fruits index</a>
</body>
</html>
```
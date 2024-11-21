<h1>
  <span class="headline">MEN Stack Error Handling</span>
  <span class="subhead">Handling Port Conflicts</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to handle port conflicts in an Express application.

## What are port conflicts?

In web development, ports are endpoints for network communications. They allow information to enter or leave a computer. Each port has a unique numerical identifier. Much like a ship can dock at a specific port in real life, each app we write will "dock" at a specific port on your computer.  Once "docked", any information going to that port will be handle by that application. This helps differentiate where information should go when multiple applications (or "services") are running on a single computer. A port conflict occurs when multiple services try to use the same port number at the same time. This leads to a situation where neither service can operate.

## Detecting port conflicts

When working with Express, you've likely encountered port conflicts when trying to run multiple Express applications at the same time. They appear as unhandled errors that cause your application to crash. The resulting error messages you see in your terminal can be daunting, especially when troubleshooting other issues. Detecting port conflicts can help you manage them more effectively.

In an Express app, `app.listen()` returns an instance of a web server. By 'listening' to the `'error'` event on this server instance, we can detect when a port conflict occurs. This gives us the opportunity to handle the error appropriately. To accomplish this, we'll use an important method in the Node.js environment called `on()`.

The [`on()`](https://nodejs.org/api/events.html#events) method can be called on a server instance and listens for events such as `'error'`.

The method accepts two arguments:

1. The name of the event we wish to listen for.
2. A callback function.

Let's chain this method to the end of the server instance, providing a callback function that logs information on any error we encounter.

Update `server.js` with the following:

```javascript
// server.js
app.listen(port, () => {
  console.log(`The express app is ready on port ${port}!`);
}).on('error', (err) => console.log(err.code));
```

Start your app with another server running in the background. 

You should see the following code in your terminal:

```plaintext
EADDRINUSE
```

The `EADDRINUSE` error code indicates a port is already in use. We can use this error code in a function designed to log issues that occur when starting the server. Our function will include a condition to check for the `EADDRINUSE` error code, and provide users with a clear and succinct message.

Update `server.js` with the following:

```javascript
// server.js
const handleServerError = (err) => {
  if (err.code === 'EADDRINUSE') {
    console.log(`Warning! Port ${port} is already in use!`);
  } else {
    console.log('Error:', err);
  }
}

app.listen(port, () => {
  console.log(`The express app is ready on port ${port}!`);
}).on('error', handleServerError);
```

Try it out!

## Resolving port conflicts

Dealing with port conflicts often involves a situation where you're not sure which application is occupying a particular port. This often occurs if you exit a project without properly shutting down its server, leaving the port it was using occupied.

If you aren't sure which port is currently in use by a Node.js app, you can use the following command to terminate all running Node.js processes on your system. This includes any Express applications that might be running:

```bash
killall node 
```

> 🚨 This command will stop all Node.js processes, which might include more than your intended target!
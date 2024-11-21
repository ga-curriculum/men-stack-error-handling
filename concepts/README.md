<h1>
  <span class="headline">MEN Stack Error Handling</span>
  <span class="subhead">Concepts</span>
</h1>

**Learning objective:** By the end of this lesson, students will understand the purpose and importance of error handling in an application.

## What is error handling?

Error handling is the process of detecting and addressing errors with code. Errors are inevitable in any application. When unexpected errors arise, they disrupt the normal flow of our applications. As developers, our role is to anticipate these errors and implement strategies to handle them accordingly. This ensures that our applications behave correctly, even when issues occur.

Generally, error handling does not refer to errors in code syntax, like missing brackets or typos. These kinds of errors usually prevent the application from running altogether until they are fixed. Error handling typically refers to the process of managing errors that occur **during the execution of an application**.

## Why is error handling beneficial?

Effective error handling is essential for maintaining the stability, security, and usability of an application. It plays a crucial role in identifying and managing unexpected issues, making our applications more reliable. Error handling not only improves the overall quality of an application, but offers several benefits for both users and developers.

- For **users**, proper error handling creates a better user experience. It prevents application crashes and provides clear feedback, preventing user confusion and frustration. Well-crafted error messages inform users about issues and suggest corrective measures when possible. This is particularly important when using interactive elements like forms.

- For **developers**, error handling simplifies the debugging process and contributes to an application's long-term stability. By detecting and logging errors effectively, developers can diagnose and resolve issues more quickly. Developers should always endeavor to write code that does not *fail silently*.

  > 📚 [*Failing silently*](https://developers.google.com/tech-writing/error-messages/error-handling#dont_fail_silently) is a scenario where an application encounters an error but does not inform the user or log the error. Always ensure your application handles errors visibly to some degree, be it through user notifications or logging. This practice is vital for both user trust and ease of maintenance.

## Types of errors in Node.js/Express

Effective error handling requires a solid understanding of the types of errors your application may encounter. Below are some common types of errors you might deal with in an Express application.

- **Runtime Errors**: These happen during the execution of your application. For example, if your code tries to read a property from an undefined variable, it causes a runtime error. These errors can crash your application if not handled.

- **Operational Errors**: These are issues related to the operation of the system, not directly caused by code. Examples include a database connection failing, the server being unable to listen on a given port, or a request taking too long to respond.

- **Promise Rejections**: When dealing with asynchronous tasks, sometimes promises can be rejected due to errors. If these rejections aren't properly handled, they can cause unexpected problems.

- **Custom Errors**: Sometimes, you might need to define your own errors that are specific to your application's logic. For instance, you might create a custom error for when a user tries to access a resource they don't have permission for.

Each of these error types requires different handling strategies to ensure your Express application runs smoothly and provides a good user experience.
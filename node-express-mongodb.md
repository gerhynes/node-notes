# Notes on Node, Express and MongoDB

## Node

Node is a JavaScript runtime that executes code outside the browser. You can use the same JavaScript syntax to write server-side code.

You can build web servers, command line tools, native apps (like VS Code), video games, drone software, and more. Node is used by everyone from Netflix to NASA.

Since Node does not run in the browser, you don't have access to browser-specific things, like the window, document and DOM APIs.

The global object in Node is `global` rather than `window`, like in the browser. This is where global methods like `setInterval` and `setTimeout` live.

Node comes with a lot of built-in modules to help you interact with the operating system and filesystem.

All methods on the filesystem module, `fs`, have synchronous and asynchronous versions. The asynchronous version always takes a completion callback as its last argument. The synchronous version will block the entire process until it has completed. For example, if you need a directory to be created before you add something into it, use `fs.mkdirSync` instead of `fs.mkdir`.

Most modules need to be imported, using `require` to make them available in scope: `const fs = require("fs")`

### Process and argv

The `process` object is a global that provides information about, and control over, the current Node process. It can tell you about where the process is running, its environmental variables, etc.

The `process.argv` property returns an array containing the command-line arguments passed when the Node process was launched. The first element will be `process.execPath` (where the executable that started the Node process is). The second element will be the path to the file being executed. The remaining elements will be any additional command-line arguments.

### Modules

In client-side JavaScript you include different JavaScript files in the same HTML document and have access to all the variables and functions, assuming you've included them in the right order.

With Node you can be very particular about what a file does and does not share.

You can require code from other files just like you can require code from a built-in module.

However, when you create a file, its contents are not just automatically available elsewhere when you require that file. You need to explicitly export from the file.

`module.exports` is the default object that is exported. You can also use `exports` as a shortcut.

You can require an entire directory. Node will look for the `index.js` file in that directory and whatever that file exports is what the directory exports.

### NPM

NPM is both (1) a library of thousands of packages published by other developers and (2) a command line tool to easily install and manage those packages in a Node project.

## Express

### Middleware

Express middleware are functions that run during the request/response lifecycle.

Each middleware can execute code as well as acess and change the request and response objects.

Middleware can end the HTTP request by sending back a response with methods like `res.send()`.

Middleware can also be chained together, one affecting another by calling `next()`.

With Express, `app.use()` lets you run code on every request.

```js
const requestTime = (req, res, next) => {
  req.requestTime = Date.now();
  next();
};

app.use(requestTime);
```

You can pass multiple callbacks to your routes. As long as they call `next()`, Express will move on to the next callback.

```js
app.get("/secret", verifyPassword, (req, res) => {
  // Do something
});
```

## MongoDB

If your application needs to persist data, you need a database.

Databases can handle large amounts of data efficiently and store it compactly. They provide tools to more easily insert, query and update data. Usually, they also offer security features and access control. They also need to be able to scale.

There are two broad categories of databses: SQL and NoSQL.

SQL databases are relational and share a Structured Query Language. You define the database's structure ahead of time.

NoSQL databases cover multiple common types: document, key-value, graph.

A document data store can be more flexible since you aren't bound to a pre-defined schema.

MongoDB is a popular document-based database commonly used with Node. It pairs well with JavaScript and has a strong community.

MongoDB uses BSON (Binary-JSON) which is faster and more space-efficient than JSON. It also supports more data types.

To insert documents into MongoDB, you insert them into a collection. If you insert into a collection that doesn't exist, it will be created.

A unique primary key `_id` is added to every item in a collection.

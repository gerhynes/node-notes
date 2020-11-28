# Node Notes

Node.js is a JavaScript runtime environment which uses the V8 JavaScript engine (which powers Google Chrome) to run JavaScript outside the browser.

A Node app runs as a single process and doesn't create a new thread for every request.

When Node performs an I/O operation (like accessing a database or the filesystem) it doesn't block the thread waiting but instead resumes the operation when the response comes back.

### Node Hello World app

```js
const http = require("http");

const hostname = "127.0.0.1";
const port = process.env.PORT || 3000;

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader("Content-Type", "text/plain");
  res.end("Hello World!\n");
});

server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}/`);
});
```

### Differences between Node.js and client-side JavaScript

With Node, you don't have access to objects provided by the browser, such as `document` or `window`. Conversely, in the browser you don't have access to Node's modules and the APIs they make available.

With Node you control the environment your code runs in and can use the latest ECMAScript features. You don't have to wait for browsers to add support.

Currently Node uses the CommonJS module system while browsers are starting to use ES Modules. So most Node code will use `require()` while browsers use `import`.

### The V8 JavaScript Engine

V8 is the JavaScript engine that powers Chrome. V8 provides the runtime environment that the JavaScript executes in. The DOM and the other web APIs are provided by the browser.

Node benefits for the race for performance between browser providers. As V8 improves, Node becomes faster.

Although JavaScript is usually considered an interpreted language, modern JavaScript engine compile it.

Since JavaScript now has to run long-lasting and heavier browser-based applications like content streaming services and interactive maps, it makes sense to spend more time getting the JavaScript ready and then have it run as performant compiled code.

### Environment Variables

The `process` core module provides Node with the `env` property which hosts all the environment variables that were set when the process started.

```js
process.env.NODE_ENV;
```

This allows you to set custom environment variables, for example different variables for development and production environments.

### The Node REPL

The `node` command lets you run Node scripts.

```js
node app.js
```

If you leave out the filename, you enter REPL mode. This environment lets you accept single expressions as user input and return the results to the console after execution.

```js
> console.log('test')
test
undefined
>
```

The first value, `test`, is the output you told the console to print, then you get undefined, which is the return value of running `console.log()`.

In REPL if you press the `tab` key it will try to autocomplete what you wrote to match an already defined variable.

REPL lets you explore JavaScript objects. For example, enter `Number .` and press `tab` and the REPL will print all the properties and methods you can access on the `Number` class.

### Accepting arguments from the command line

You can pass arguments when invoking a Node application

```js
node app.js annemarie
```

The `argv` property on the `process` object provides an array containign all the command line invocation arguments.

The first element is the full path of the `node` command.

The second element is the full path of the file being executed.

All additional arguments are included from the third position going forward.

You can skip the first two params using `slice` on `argv`.

```js
const args = process.argv.slice(2);
```

### Outputting to the command line

Node includes a `console` module which gives you useful ways to interact with the command line (basically the same as the `console` object in the browser).

The most common method is `console.log()` which prints a string to the console.

You can format this by passing in variables and a format specifier.

```js
console.log("My %s is %d years old", "dog", 7);
```

- `%s` formats a variable as a string
- `%d` formats a variable as a number
- `%i` formats a variable as its integer part only
- `%o` formats a variable as an object

`console.log` prints the standard output, `stdout`, while `console.error` prints the `stderr` stream instead.

`console.trace()` will print the stack trace of a function showing you how you reached that part of the code.

You can use `time()` and `timeEnd()` to calculate how much time a function takes to run.

```js
const doSomething = () => console.log("test");
const measureDoingSomething = () => {
  console.time("doSomething()");
  //do something, and measure the time it takes
  doSomething();
  console.timeEnd("doSomething()");
};
measureDoingSomething();
```

You can colour the text in the console using escape sequences, a set of characters which identify a colour.

```js
console.log("\x1b[33m%s\x1b[0m", "hi!");
```

You can also use packages such as [Chalk](https://github.com/chalk/chalk) or [Progress](https://github.com/visionmedia/node-progress) to style your output or create a progress bar in the console.

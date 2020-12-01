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

### Accepting input from the command line

Since version 7, Node has provided the `readline` module which gets input from a readable stream, such as the `process.stdin` stream, and shows the input to the terminal one line at a time.

The code below asks for a username, and once the text is entered and the user presses enter, shows a greeting.

```js
const readline = require("readline").createInterface({
  input: process.stdin,
  output: process.stdout
});

readline.question(`What's your name?`, (name) => {
  console.log(`Hi ${name}!`);
  readline.close();
});
```

You can create more complex interactions, with multiple choice questions, confirmation, and more, using a package such as `Inquirer.js`.

### The Module System

A Node file can import functionality that is exposed by another Node file using `require()`.

Functionality must be exposed before it can be imported by other files. This is what the `module.exports` API does.

When you assign an object or function as an `exports` property, you expose it and allow it to be imported to other parts of the app, or other apps entirely.

You can assign an object to `module.exports` (an item provided by the module system), and your file will export just that object.

```js
const book = {
  title: "The Lord of the Rings",
  author: "J.R.R. Tolkien"
};

module.exports = book;

// in the other file
const book = require("./book");
```

Or you can add the exported object as a property on `exports`. This lets you export multiple objects, functions or data.

```js
const book = {
  title: "The Lord of the Rings",
  author: "J.R.R. Tolkien"
};

exports.book = book;

// in the other file
const items = require("./items");
item.book;
```

`module.exports` exposes the object it points to.

`exports` exposes the properties of the object it points to.

## npm

npm, the standard package manager for Node, manages dependencies for your application installing them in the `node-modules` folder and keeping track of them in the `package.json` file.

Adding the `--save` flag adds the package to the `package.json` file `dependencies`, while adding the `--save-dev` adds the package to the `devDependencies`. `devDependencies` are usually development tools, while `dependencies` are bundled with the app in production.

npm lets you specify which version of a package to use. Sometimes a library is only compatible with a major release of another library. Or a bug in the latest release of a library causes issues and you want to wait for a fix before upgrading.

The `package.json` lets you specify command line tasks, such as running a bundler, you can run using a script.

```js
{
  "scripts": {
    "watch": "webpack --watch --progress --colors --config webpack.conf.js",
    "dev": "webpack --progress --colors --config webpack.conf.js",
    "prod": "NODE_ENV=production webpack -p --config webpack.conf.js",
  },
}
```

By default, npm installs packages locally in the `node_modules` folder of the current project. Packages can be installed globally using the `-g` flag.

Once installed a package can be imported into your programme using `require`.

```js
const _ = require("lodash");
```

### package.json

The `package.json` file is a manifest (of sorts) for your project. It can contain properties describing the project, from name, version and repository to licence, contributors and issues. It's also the central repository for the configuration of tools.

### package-lock.json

The `package-lock.json` file (added in version 5 of npm) keeps track of the exact version of every package that's installed so that the project can be reproduced exactly.

The dependencies in `package-lock.json` are updated when you run `npm update`.

The `package.json` sets which versions you want to upgrade to (patch or minor), using semver notation.

- Writing ~0.13.0 will only update patch releases: 0.13.1 is ok, but 0.14.0 is not.
- Writing ^0.13.0 will update patch and minor releases: 0.13.1, 0.14.0 and so on.
- Writing 0.13.0 is the exact version that will always be used.

`npm list` will show the latest versions of all installed npm packages, including their dependencies.

`npm list --depth=0` will only show the top-level packages, not their dependencies.

You can install a specific version of an npm package using:

```js
npm install <package>@<version>
```

`npm view <package> versions` will list all previous versions of a package.

When you install a package using `npm install <packagename>` the latest available version is downloaded, and it's entry is added to the `package.json` and `package-lock.json`.

To find new releases for packages, run `npm outdated`.

To update to a new major version of all packages, install the `npm-check-updates` package globally, then run `ncu -u` to upgrade the version hints in the `package.json`. Now run `npm update`.

### Semantic Versioning in npm

npm packages use semantic versioning for their version numbering.

All versions have three digits for major version, minor version, and patch version, for example, `2.24.91`.

The rules about updating packages are shown through symbols:

- `^` you accept patch and minor releases. For example, from `^1.13.0` to `1.13.1` or `1.14.0`

- `~` you accept patch releases. For example, from `~0.13.0` to `0.13.1` but not `0.14.0`

- `>` you accept any version higher than the one you specify

- `>=` you accept any version equal to or higher than the one you specify

- `<=` you accept any version equal to or lower than the one you specify

- `<` you accept any version lower than the one you specify

- `=` you accept that exact version

- `-` you accept a range of versions. For example, `2.1.0 - 2.6.2`

- `||` you combine sets. For example, `< 2.1 || > 2.6`

If there's no symbol, you accept only the specified version.

### Uninstalling packages

Use `npm uninstall` to remove a package from `node_modules`. Add the `-S`/`--save` or `-D`/`--save-dev` to also remove the reference to it in `package.json`.

### Local or global package installation

In general packages should be installed locally to prevent compatibility issues.

A package should be installed globally when it provides an executable command that you run from the CLI

### npm dependencies and devDependencies

Adding `-D`/`--save-dev` when installing packages saves them to `devDependencies` in the `package.json`.

Development dependencies are intended for development-only tools, like testing libraries, bundlers, or transpilers.

To only install production dependencies use `npm install --production`.

### npx

npx is a package runner that lets you run code built with Node and published through npm.

npx lets you run executable commands without knowing the exact path or requiring the package to be installed globally.

npx also lets you run commands without first installing them, such as `create-react-app`.

## The Node Event Loop

The JavaScript code run by Node runs on a single thread. There is just one thing happening at a time.

This is a helpful limitation as it simplifies programmes and avoids concurrency issues.

You need to write Node code to avoid anything that could block the thread, like synchronous network calls or infinite loops.

In most browsers, there is an event loop for every browser tab to prevent heavy processing from blocking the entire browser.

Any JavaScript code that takes too long to return back control to the event loop will block the execution of any JavaScript code on the page, even blocking the UI thread, and the user will not be able to click around, scroll the page, and so on.

Almost all I/O primitives in JavaScript, such as network requests or filesystem operations, are non-blocking. Being blocking is the exception. This is why JavaScript has been based so much on callbacks and more recently async/await.

### The call stack

The JavaScript call stack is a last in/first out queue.

The event loop continuously checks the call stack to see if there's any function that needs to run. It adds any function it finds to the call stack and executes each one in order.

### The message queue

The message queue is a list of messages to be processed. A callback function triggered by `setTimeout` is placed here, as are user-initiated events like clicks or keyboard events, as well as fetch responses.

The event loop gives priority to the call stack, and first processes everything it finds there, then it picks up things from the message queue.

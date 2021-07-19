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

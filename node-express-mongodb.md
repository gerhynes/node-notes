# Node

Node is a JavaScript runtime that executes code outside the browser. You can use the same JavaScript syntax to write server-side code.

You can build web servers, command line tools, native apps (like VS Code), video games, drone software, and more. Node is used by everyone from Netflix to NASA.

since Node does not run in the browser, you don't have access to browser-specific things, like the window, document and DOM APIs.

The global object in Node is `global` rather than `window`, like in the browser. This is where global methods like `setInterval` and `setTimeout` live.

Node comes with a lot of built-in modules to help you interact with the operating system and filesystem.

### Modules

In client-side JavaScript you include different JavaScript files in the same HTML document and have access to all the variables and functions, assuming you've included them in the right order.

With Node you can be very particular about what a file does and does not share.

You can require code from other files just like you can require code from a built-in module.

When you create a file, its contents are not just automatically available elsewhere when you require that file. You need to explicitly export from the file.

`module.exports` is the default object exported.

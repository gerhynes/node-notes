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

NPM is both

1. a library of thousands of packages published by other developers and
2. a command line tool to easily install and manage those packages in a Node project.

## Express

Express is a web framework for Node. It helps you to

- start up a server to listen for requests
- parse incoming requests
- match those requests to particular routes
- create http responses and associated content

A HTTP request is not a JavaScript object, it's text. Express turns in into an object.

On every incoming request you have access to an object representing the incoming request and an object representing the outgoing response.

The objects have methods that you can make use of, for example `res.send` which will generate and send a HTTP response.

Express takes incoming requests and a path and matches them to a response.

```js
app.get("/posts", (req, res) => {
  res.send("Posts!");
});
```

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

### Error Handling

Working with Express, a lot of errors will be due to incomplete data, problems interacting with your database, and proplems connecting to APIs.

Express comes with a built-in error handler which will respond when an error is thrown. It includes a status code and stack trace.

You can define error-handling middleware functions which take an error as their first parameter.

```js
app.use((err, req, res, next) => {
  console.error(err);
  res.status(500).send("Something went wrong");
});
```

Express is set up so that if you execute `next()` and pass something in, it knows it's an error.

Often you'll want to define your own error class with methods allowing you to respond to different errors with a status code and message. The default error-handler will use this status.

```js
class AppError extends Errror {
  constructor(status, message) {
    super();
    this.status = status;
    this.message = message;
  }
}

app.get("/admin", (req, res) => {
  throw new AppError(403, "You are not an admin!");
});
```

#### Handling async errors

For errors returned from asynchronous functions invoked by route handlers and middleware, you must pass them to `next()`, where Express will catch and process them.

```js
app.get("/products/:id", async (req, res, next) => {
  const { id } = req.params;
  const product = await Product.findById(id);
  if (!product) {
    return next(new AppError("Product not found", 404));
  }
  res.render("products/show", { product });
});
```

It's good practice to add a try-catch block to all async functions to catch errors thrown by Mongoose or external APIs.

```js
app.post("/products", async (req, res, next) => {
  try {
    const newProduct = new Product(req.body);
    await newProduct.save();
    res.redirect(`/products/${newProduct._id}`);
  } catch (error) {
    next(error);
  }
});
```

Even better is to create a utility function for handling asynchronous errors.

```js
function wrapAsync(fn) {
  return function (req, res, next) {
    fn(req, res, next).catch((err) => next(err));
  };
}

app.get(
  "/products/:id",
  wrapAsync(async (req, res, next) => {
    const { id } = req.params;
    const product = await Product.findById(id);
    if (!product) {
      throw new AppError("Product not found", 404);
    }
    res.render("products/show", { product });
  })
);
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

Once you have MongoDB installed locally, to start using it, you connect a `mongo.exe` shell to the running MongoDB instance.

On Windows, enter `"C:\Program Files\MongoDB\Server\5.0\bin\mongo.exe"` into a Command Prompt with Administrative priviledges.

### CRUD Operations

To insert documents into MongoDB, you insert them into a collection. If you insert into a collection that doesn't exist, it will be created.

Using `db.collection.insert()` you can pass in a single document (a JavaScript object) or an array of documents. You can also use `db.collection.insertOne()` or `db.collection.insertMany()`.

A unique primary key `_id` is added to every item in a collection. You can also specify an `_id`.

### Mongoose

Mongoose is an ODM (Object Data Mapper / Object Document Mapper). It maps documents coming from a database into usable JavaScript objects.

Mongoose proivdes ways for you to model your application data and define schemas. It can also validate data and build complex queries from within JavaScript.

```js
const mongoose = require("mongoose");

mongoose
  .connect("mongodb://localhost:27017/test", {
    useNewUrlParser: true,
    useUnifiedTopology: true
  })
  .then(() => {
    console.log("DB Connection Open");
  })
  .catch((err) => {
    console.error(err);
  });
```

You can single out specific Mongoose errors and handle them seperately.

```js
app.use((err, req, res, next) => {
  console.log(err.name);
  if (err.name === "ValidationError") {
    err = handleValidationError(err);
  }
  next(err);
});
```

### Models

Models are JavaScript classes that you make with the assistance of Mongoose that represent information in a collection in a MongoDB database.

You need to define a model for every resource you plan on working with.

For each model you need to define a schema. A schema is a mapping of different collection keys in MongoDB to different types in JavaScript.

```js
const movieSchema = new Mongoose.Schema({
  title: String,
  year: Number,
  score: Number,
  rating: String
});

const Movie = mongoose.model("Movie", movieSchema);
```

Mongoose will take the name of the model "Movie" and create a collection "movies".

You save the model to a JavaScript class and can then make new instances of your class and save them to your database.

```js
const amadeus = new Movie({
  title: "Amadeus",
  year: 1984,
  score: 9.2,
  rating: "R"
});

amadeus.save();
```

You can insert multiple documents into a colection using the `insertMany` method on the model. This takes an arrray of objects and returns a promise.

```js
Movie.insertMany([
  { title: "Amelie", year: 2001, score: 8.3, rating: "R" },
  { title: "Alien", year: 1979, score: 8.1, rating: "R" },
  { title: "The Iron Giant", year: 1999, score: 7.5, rating: "PG" },
  { title: "Stand By Me", year: 1986, score: 8.6, rating: "R" },
  { title: "Moonrise Kingdom", year: 2012, score: 7.3, rating: "PG-13" }
])
  .then((data) => {
    console.log("It worked");
    console.log(data);
  })
  .catch((err) => {
    console.error(err);
  });
```

There are multiple ways to find records with Mongoose.

### Mongo Relationships

In relational databases you have independent tables and reference relationships between them to avoid storing duplicate data.

You can have one to many relationships, for example, a user to their comments, where one user has many comments but each comment is connected to just one user.

You can also have many to many relationships, for example, movies to actors, where you would need a third table for roles which would connect movies to actors.

MongoDB gives you a lot of options for how to structure your data but this comes wth its own challenges.

#### One to Few

In the case of one to few relationships, the easiest approach is to embed the data directly in the document.

For example, a person usually has a limited number of addresses and your primary way of interacting with an address is through a user. So it makes sense to store them in the user document.

```js
{
  name: "Tommy Cash",
  savedAddresses: [
    { street: "Rahukohyu 3", city: "Talinn", country: "Estonia" },
    { street: "Ravala 5", city: "Talinn", country: "Estonia" },
  ]
}
```

Mongoose treats nested documents as their own embedded schemas and gives them a unique id by default. You can turn this off.

#### One to Many

When you have a relationship between many documents, one option is to store your data seperately but then store references to document ids somewhere inside the parent document. This is similar to how relational databases work.

```js
{
  name: "Full Belly Farms",
  location: "Gurinda, CA",
  products: [
    OBjectId("2819781267781"),
    OBjectId("1287526267789"),
    OBjectId("5563398021890"),
  ]
}
```

```js
const farmSchema = new Schema({
  name: String,
  location: String,
  products: [{ type: Schema.Types.ObjectId, ref: "Product" }]
});

const farm = new Farm({ name: "Full Belly Farms", location: "Gurinda, CA" });
farm.products.push(melon);
farm.save();
```

#### Mongoose Populate

By default, Mongoose just saves the ids of embedded documents. The `ref` option tells Mongoose which model the id is connected to.

To query for a document and its embedded documents, use the `populate()` method.

```js
Farm.findOne({ name: "Full Belly Farms" })
  .populate("products")
  .then((farm) => console.log(farm));
```

#### One to Bajillions

With a large volume of data, thousands or more documents, it's more efficient to store a reference to the parent on the child document.

```js
{
  tweetText: "I just crashed my car because I was tweeting",
  tags: ["yolo", "eejit", "carcrash"],
  user: ObjectId("2133243243")
}
```

```js
const userSchema = new Schema({
  username: String,
  handle: String
});

const tweetSchema = new Schema({
  text: String,
  likes: Number,
  user: { type: Schema.Types.ObjectId, ref: "User" }
});

const User = mongoose.model("User", userSchema);
const Tweet = mongoose.model("Tweet", tweetSchema);

const user1 = new User({ name: "chickenfan99", handle: "@chickenfan99" });
const tweet1 = new Tweet({ text: "I love my chicken family", likes: 0 });
tweet1.user = user1;
user1.save();
tweet1.save();

const t1 = await Tweet.findOne({}).populate("user");

// You can also choose to only populate certain fields
const t2 = await Tweet.findOne({}).populate("user", "username");
```

To complicate things further, you could store a reference on the parent and on the child document so you could go both directions if necessary.

You could also choose to duplicate some information if it is often queried together, for example storing the username, rather than just the userId, on a tweet.

#### Mongo Schema Design

To recap:

- you can embed data in a one to many relationship
- you can have two seperate entities and store a reference on the one side
- you can have two seperate entities and store a reference on the many side
- you can denormalize as many fields as you like, duplicating data on both sides, if it makes sense in your application

MongoDB's [advice](https://www.mongodb.com/blog/post/6-rules-of-thumb-for-mongodb-schema-design-part-3) is to

- favor embedding unless there is a compelling reason not to.
- needing to access an object on its own is a compelling reason not to embed it.
- Arrays should not grow without bound. If there are more than a couple of hundred documents on the “many” side, don’t embed them; if there are more than a few thousand documents on the “many” side, don’t use an array of ObjectID references.
- Consider the write/read ratio when denormalizing. A field that will mostly be read and only seldom updated is a good candidate for denormalization: if you denormalize a field that is updated frequently then the extra work of finding and updating all the instances is likely to overwhelm the savings that you get from denormalizing.
- As always with MongoDB, how you model your data depends – entirely – on your particular application’s data access patterns. You want to structure your data to match the ways that your application queries and updates it.

#### Deleting related documents

If you want to delete all associated documents when you delete a document, you can do it manually by finding and deleting them or you can use a Mongoose middleware.

Mongoose's middleware, also called pre and post hooks, let you run functions before or after an action happens. They are totally seperate from Express middlware and do not need you to call `next()`.

For example, `findByIdAndDelete()` triggers the middleware `findOneAndDelete()`.

With a query middleware, you need to wait until after the query was completed so that you have access to the document that was found.

```js
// Delete all associated products after a farm is deleted
farmSchema.post("findOneAndDelete", async function (farm) {
  if (farm.products.length) {
    const res = await Product.deleteMany({ _id: { $in: farm.products } });
    console.log(res);
  }
});
```

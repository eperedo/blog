---
title: Multiple database connections in objectionjs and knex
date: 2019-12-28 14:02:55
---

Normally you have one database for your app, but when your app starts to grow
it's normal to create more databases to handle more traffic in an efficient way.
So the question is:

### Is objectionjs ready for this?

Spoiler Alert: **YES**.

Let's assume we have 2 databases called: **db_one** and **db_two**

and what we want to achieve is to connect one of our objectionjs Models to the database **db_one** and the other one to the database **db_two**.

Let's start creating our models.

```js
// models/Product.js
const { Model } = require("objection");

class Product extends Model {
  static get tableName() {
    return "products";
  }
}

module.exports = Product;

// models/Category.js

const { Model } = require("objection");

class Category extends Model {
  static get tableName() {
    return "categories";
  }
}

module.exports = Category;
```

Nothing new here, but how can we tell objectionjs to give our models a specific database connection?

### Introducing bindKnex

Every objectionjs model has this method called **bindKnex** and it accepts one parameter: a valid **knexjs** database connection. And it will return the same model, but binded to a knexjs connection.

Let's see the code to have a better understanding of it.

```js
// index.js

const Knex = require("knex");

// connection for our db_one database
const cnDbOne = Knex({
  client: "mysql2",
  connection: {
    database: "localhost",
    user: "root",
    password: "root",
    database: "db_one"
  }
});

// connection for our db_two database
const cnDbTwo = Knex({
  client: "mysql2",
  connection: {
    database: "localhost",
    user: "root",
    password: "root",
    database: "db_two"
  }
});

// require our models

const ProductModel = require("./models/Product.js");

const CategoryModel = require("./models/Category.js");

const Product = ProductModel.bindKnex(cnDbOne);

const Category = CategoryModel.bindKnex(cnDbTwo);

// making queries

// db_one
// select * from products
Product.query().then(console.log);

// db_two
// select * from categories
Category.query().then(console.log);
```

Now we can execute our application

```bash
node index.js
```

and each model will make a query to a different database.

We can easily check this using the **SHOW PROCESSLIST** sql query in our mysql terminal.

```sql
SHOW PROCESSLIST;
```

![Terminal console showing multiple database connections](https://eperedo-blog.s3.amazonaws.com/multiple-database-connection-compressor.png)

and you can see our app has generated two connections for each database.

That's it! 🚀

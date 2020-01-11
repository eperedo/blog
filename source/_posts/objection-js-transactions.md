---
title: Multiple SQL Inserts and Transactions with Objection Js
date: 2020-01-11 18:00:00
---

Let's assume we have an application where we need to save the information about the sales of a company. For that purpose we have will create a database and two different tables:

```sql
CREATE DATABASE sales_company;

USE sales_company;

CREATE TABLE `sales` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `total` decimal(18,2) NOT NULL,
  `subtotal` decimal(18,2) NOT NULL,
  `taxes` decimal(18,2) NOT NULL,
  PRIMARY KEY (`id`)
);

CREATE TABLE `sales_details` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `sale_id` int(11) NOT NULL,
  `quantity` int(11) NOT NULL,
  `price` decimal(18, 2) NOT NULL,
  `product` text NOT NULL,
  PRIMARY KEY (`id`),
  KEY `sale_id` (`sale_id`),
  CONSTRAINT `sales_details_ibfk_1` FOREIGN KEY (`sale_id`) REFERENCES `sales` (`id`)
);
```

First table called **sales** save information about the amounts of the sale and in the table **sales_details** we are going to write the information of the product.
Of course you need more data for in the real world, but for the purposes of this demo it is good enough.

### Defining Objection Models

Since we are using Objection as our ORM let's start creating the two models, one for each table.

```js
// Sale.js
const { Model } = require("objection");

class Sale extends Model {
  static get tableName() {
    return "sales";
  }
}

module.exports = Sale;
```

And now the model for the **sales_details** table

```js
// SaleDetail.js
const { Model } = require("objection");

class SaleDetail extends Model {
  static get tableName() {
    return "sales_details";
  }
}

module.exports = SaleDetail;
```

Great, but now we need to establish the relationship between both models. In order to do that let's use the **relationMappings** method in the **Sale.js** file.

```js
// Sale.js
const { Model } = require("objection");
const path = require("path");

class Sale extends Model {
  static get tableName() {
    return "sales";
  }

  // this is the new code
  static get relationMappings() {
    return {
      details: {
        relation: Model.HasManyRelation,
        modelClass: path.join(__dirname, "SaleDetail"),
        join: {
          from: "sales.id",
          to: "sales_details.sale_id"
        }
      }
    };
  }
}

module.exports = Sale;
```

We are defining a **details** property (you can name this variable whatever you want) for the Sale model and establishing the relationship between both tables through the **id** column from the **sales** table
to the **sale_id** field of the **sales_details** table.

### Insert records in two tables using insertGraph

Now we can use the **insertGraph** method to insert data in both tables at the same time.

```js
// Let's create the connection to our database
// connection.js
const { Model } = require("objection");
const Knex = require("knex");

const knex = Knex({
  client: "mysql2",
  debug: true,
  connection: {
    database: "sales",
    password: "root",
    user: "root",
    host: "localhost"
  }
});

Model.knex(knex);
```

Ok now let's create an object that will represent a new sale

```js
const newSale = {
  subtotal: 10,
  taxes: 8,
  total: 18,
  // property details because that's how we
  // call it in the relationMappings
  details: [
    {
      product: "Tomato",
      quantity: 1,
      price: 4
    },
    {
      product: "Potato",
      quantity: 2,
      price: 3
    }
  ]
};
```

A simple javascript object with all the information for our tables, let's make the insert

```js
// multiple-inserts.js
require("./connection");
const Sale = require("./Sale.js");

async function saveSale() {
  const newSale = {
    subtotal: 10,
    taxes: 8,
    total: 18,
    // property details because that's how we
    // call it in the relationMappings
    details: [
      {
        product: "Tomato",
        quantity: 1,
        price: 4
      },
      {
        product: "Potato",
        quantity: 2,
        price: 3
      }
    ]
  };
  const saleInserted = await Sale.query().insertGraph(newSale);
  console.log(`New Sale Id is ${saleInserted.id}`);
  return saleInserted;
}

saveSale();
```

Executing this file will show us in the terminal 3 different insert sql queries.

![3 insert sql queries](https://eperedo-blog.s3.amazonaws.com/objection-js/multiple-insert.PNG)

That's great, but you know things in real life can be messy sometimes, what about if we have some bug in our code and the quantity of one product get the value of null? Let's see what happen.

```js
// multiple-insert-fail.js
require("./connection");
const Sale = require("./Sale.js");

async function saveSaleNullQuantity() {
  const newSale = {
    subtotal: 10,
    taxes: 8,
    total: 18,
    // property details because that's how we
    // call it in the relationMappings
    details: [
      {
        product: "Tomato",
        // some way this value is null 😭
        quantity: null,
        price: 4
      },
      {
        product: "Potato",
        quantity: 2,
        price: 3
      }
    ]
  };
  const saleInserted = await Sale.query().insertGraph(newSale);
  console.log(`New Sale Id is ${saleInserted.id}`);
  return saleInserted;
}

saveSaleNullQuantity();
```

Running this script will throw an error because the quantity column does not allow nulls.

![Sql Error](https://eperedo-blog.s3.amazonaws.com/objection-js/multiple-insert-fails.PNG)

Ok! An error, but let's see our **sales** table just in case.

![Sales Table After Error](https://eperedo-blog.s3.amazonaws.com/objection-js/multiple-insert-fails-database.PNG)

You can see the records in the **sales** and **sales_details** table were created! Not cool objection! Not cool! 😒

You are mad now, but you remember the introduction to SQL course in high school and realize that all was your fault! If you want to avoid this kind of situations you need to use an SQL TRANSACTION!

### Transactions in Objection.js

Creating a transaction is kinda easy

```js
// multiple-insert-with-transaction.js
require("./connection");
const Sale = require("./Sale.js");

async function saveSaleWithTransaction() {
  const newSale = {
    subtotal: 10,
    taxes: 8,
    total: 18,
    // property details because that's how we
    // call it in the relationMappings
    details: [
      {
        product: "Tomato",
        // some way this value is null 😭
        quantity: null,
        price: 4
      },
      {
        product: "Potato",
        quantity: 2,
        price: 3
      }
    ]
  };
  try {
    const tx = await Sale.transaction(trx => {
        const saleInserted = await Sale.query(trx).insertGraph(newSale);
      return saleInserted;
    });
  } catch(err) {
    // Here transaction has already been rolled back
    console.log(err);
  }
}

saveSaleWithTransaction();
```

Time to run this script and an error again, but now you can verify that none record was inserted to the tables 🎊 .

![No records were inserted](https://eperedo-blog.s3.amazonaws.com/objection-js/multiple-insert-fails-database.PNG)

A few things about the **transaction** method of the Sale model

- You can use any objection model inside the transaction function.
- The **trx** variable always must be passed to the **query** method of all the models that are called inside the transaction function
- The transaction will be consider **committed** if the promise function is resolved, that's why I return the **saleInserted** object at the end.
- If any error is thrown inside the function it will automatically \*_rollback_ the transaction.
- I am using a MySql database but you can use any supported database for objection like postgres or sqlite.

You can clone and play with the code in the github repo: https://github.com/eperedo/objectionjs-examples/tree/master/src/multiple-insert-transactions

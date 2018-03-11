---
title: Objection ORM (knex big brother)
date: 2018-03-11 22:58:00
---

Using knex for my [migrations](https://blog.eperedo.com/2018/03/09/knex-migrations/) was easy and fast. Now I need to retrieve data from those tables and I can use knex to do that as well, but [Knex](http://knexjs.org) has a big brother who can do my job even more fun.

### Objection.Js

According to their website:

```quote
Objection.js is built on an SQL query builder called knex. All databases supported by knex are supported by objection.js. SQLite3, Postgres and MySQL are thoroughly tested.
```

Basically I can define classes and use Objection to map that classes to a table. Objection gives me a lot of great methods who allows me to interact with the database and make simple/complex SQL queries in a clean way.

### Installation

Since I already have knex and mysql installed the only left package to add is objection it self.

```bash
npm install objection -S
```

### Setup Objection with Knex

Time to tell Objection what knex connection must use since Objection is her big brother the code is pretty short

```javascript
// objection.js
const { Model } = require('objection');
const Knex = require('knex');
const knexfile = require('./knexfile');
const knexConfig = Knex(knexfile['development']);
Model.knex(knexConfig);
```

First, I define two variables to references Objection Model and knex. A third variable is needed to know the configuration of the database (I already created this configuration in a previous post).
Finally I will use the **knex** method of Objection to tell him what is the information of the database I will use.

At this point Objection have all the information and is ready to connect to the database.

### Classes

Objection needs classes in order to identify the tables in the database. I will create one class for my ticket types table.

```javascript
// TicketType.js
const { Model } = require('objection');

class TicketType extends Model {
	static get tableName() {
		return 'ft_ticket_types';
	}
}

module.exports = TicketType;
```

That's it. Every class must extends from Model, this give objection the power to supercharge our Class with awesome methods to interact with the database.
Since Objection is great, smart and good looking He can guess the table. So I have to tell him the name of the table. That's why I am using the **tableName** method. The only thing to do inside this function is return a string with the name of the table.

### Making Queries

Time to get data from my table, I will make a simple query for now. I need all the records from the table.

```javascript
//queries.js
const objection = require('./objection');
const TicketType = require('./TicketType');
TicketType.query()
	.select('id', 'name', 'code')
	.then(ticketTypes => {
		console.log('Ticket Types', ticketTypes);
	});
```

First I need to call the objection configuration (this file must be required only once in any application). Now I have created a variable for the class **TicketType**. That's the hard part, making queries with objection is so easy. I need to call the **query** method and the magic starts, this method will give access to a powerful and fun methods to get/set data from the table.
In this case I want all the records from the table, so I will use the **select** method who takes as a parameters the columns I want to show from my table.

All methods in Objection return a promise so I can chain **then** method and it will return the result of the query. In this case the result is the records I created in my [seed]() file.

```bash
node queries.js
Ticket Types [ TicketType { id: 1, name: 'Customer', code: 'CUS' },
  TicketType { id: 2, name: 'VIP', code: 'VIP' },
  TicketType { id: 3, name: 'Regular', code: 'REG' } ]
```

I will explore other methods in a next post for sure 🤓

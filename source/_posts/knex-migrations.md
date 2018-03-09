---
title: Sql migrations using Knex
---

I need two tables for my next app. **ft_ticket_types** and **ft_tickets**.
Sometimes I like doing things the right way and sometimes I like to lie 🙊.

So I will use [knex](https://knexjs.org) because is so freaking awesome.

### MySql

I feel kinda nasty today so I will use [MySql](https://www.mysql.com/) for the database. A couple of [docker](https://docker.com) commands and I have
a mysql instance along with an adminer website because I do not feel THAT nasty to use the terminal 😅
After logging to adminer I will create my database: **knex_tickets** and it is time to make magic with knex.

### Installing knex

```bash
npm install knex mysql -S
```

I have just install two packages from npm: knex and mysql. The -S is the shorcut for --save.

### Knex init

Besides of been a great sql builder and migration tool, knex has a little cli incorporated.
One of her thousand commands (yes, Knex is a woman 👩‍✈) generate a configuration file for me.

```bash
npx knex init
```

It will create a **knexfile.js** file with some basic configuration in order to connect to a database.
I just replace some vaues and my file looks like the following:

```javascript
module.exports = {
	development: {
		client: 'mysql',
		connection: {
			database: 'knex_tickets',
			user: 'root',
			password: '1234',
		},
	},
};
```

Please do not hack into my database 😒.

### Knex Migrations

Another great command is migrate:make it will generate a migration for free, but it demands a name as a parameter.

```bash
npx knex migrate:make create-type-ticket
```

knex is smart and generate a folder called **migrations** and inside of it a file with the migration I wanted
**20180308221110_create-type-ticket.js**.

### Writing migrations

Inside the file I can see two functions: up and down. The up will execute every time I execute a migration and the down when the rollback command is called.

```javascript
exports.up = (knex, Promise) => {
	return knex.schema.createTable('ft_ticket_types', table => {
		table.increments();
		table.text('name').notNullable();
		table.text('code').notNullable();
	});
};

exports.down = function(knex, Promise) {
	return knex.schema.dropTable('ft_ticket_types');
};
```

First, both the up and down functions always must return a Promise. All the methods to interact with the database are inside the schema object like the **createTable** method that allow me to... well create a table 😒.

This method take 2 parameters: the name of the table and a function where I can write the fields I want for my new shiny table.
In the knex docs I can see a lot of methods which can generate the table content. I am using only two because I am lazy.
Basically I am saying this to knex: I want a table called ft_ticket_types and the table will contains 3 fields. An autoincrement **id** field, two **text** columns for the name and the code.
I do not need to set the name for the ID field because the **increments** function will use that value by default.

### Executing migrations

Ok, everything looks great but where in the hell is my table? Time to run the migration using another awesome command.

```bash
npx knex migrate:latest
```

and it gives me good news!

```bash
Batch 1 run: 1 migrations
```

If I go to my adminer website I can see my new table.

![knex migration](https://s3.amazonaws.com/blog/knex-migrations/first-migration.png)

Also two extra tables knex use to know what migrations are already executed.

### I have made a huge tiny mistake

What about if I wrote a migration, executed but I do not want it anymore. Well looks like knex is so awesome she can make a rollback and save my ass.

```bash
npx knex migrate:rollback
```

Easy! Now my table is gone.

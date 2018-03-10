---
title: Relations between tables in Knex
date: 2018-03-10 22:58:00
---

I need another table **ft_tickets**, but in this case the table has a field called **type_id** which is related to the **ft_ticket_types**.

Since I already know [how to write a migration](https://blog.eperedo.com/2018/03/09/knex-migrations/) it should be easy.

```javascript
exports.up = (knex, Promise) => {
	return knex.schema.createTable('ft_tickets', table => {
		table.increments();
		table
			.integer('type_id')
			.unsigned()
			.notNullable();
		table.integer('number').notNullable();
		table.timestamps();
	});
};

exports.down = function(knex, Promise) {
	return knex.schema.dropTable('ft_tickets');
};
```

Great, a couple of two things here:

1.  What about the relationship 🤔. Knex can't guess the field **type_id** is related to other table.
2.  The **\*timestamps** method will generate automatically two extra fields for my table: created_at, updated_at.

### Foreign Key in Knex

Creating FK using knex is easy as everything in Knex is. As I thought, there is a method who allows me to do that.

```javascript
table
  .integer('type_id')
  .unsigned()
  .notNullable();
table
  .foreign('type_id')
  .references('id')
  .inTable('ft_ticket_types');
};
```

So first I define the field who will have the FK relation. Then I call the **foreign** function and pass as a parameter the name of the field. Now I need to call the **references** function which need as a parameter the name of the field of the source table, in this case is the ID. Last - but not least - using the **inTable** function I can tell to knex the table who contains the referenced field.

### Running Migrations

```bash
npx knex migrate:latest
```

Return the message

```bash
Batch 2 run: 1 migrations
```

To the adminer cave!

![Foreign Key with knex](https://s3.amazonaws.com/blog/knex-migrations/foreign-key-knex.png)

Another great day thanks to [knex](http://knexjs.org)

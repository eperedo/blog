---
title: Relationships in objection.js
date: 2018-03-12 21:00:00
---

Now I need a list of tickets but including the information of their type. Looks like it is a work for the **eager** method.

### Define Relations in Objection

First I need to tell objection how my tables are related. For this I need to use the **relationMappings**
method who will need an object with the definition of all my relationships.
In this case I only need one relationship since a ticket has one type.

```javascript
const { Model } = require('objection');

class Ticket extends Model {
	static get tableName() {
		return 'ft_tickets';
	}

	static get relationMappings() {
		return {
			type: {
				relation: Model.HasOneRelation,
				modelClass: `${__dirname}/TicketType.js`,
				join: {
					from: 'ft_tickets.type_id',
					to: 'ft_ticket_types.id',
				},
			},
		};
	}
}

module.exports = Ticket;
```

This is my Ticket Model, inside the **relationMappings** function I have defined an object called **type**. This name can be anything I want, but since a ticket can have a type looks like a good name 😅
Now I will define what kind of relation the tables has. In this case I will use the **HasOneRelation** value.
Good, the second information objection needs is the path to the Model what I want to relate my current Model.
It can be a relative or absolute path, I like absolute paths so I am using the **\_\_dirname** variable.
Now the **join** method will contain the fields who define the relation between the tables. In this case the table **ft_tickets** has the type_id field and this field is related with the **ID** of the **ft_ticket_types** table.

### The eager function

Finally I can use the **eager** method to make a query including the relationship.

```javascript
Ticket.query()
	.eager('type')
	.select('id', 'number', 'type_id')
	.then(tickets => {
		console.log('Types', tickets);
	});
```

The eager method takes as a parameter the name of the relationship who has been defined in the **relationMappings** function. Since I choose the name **type** in there I must use the same name inside the eager method.
Inside the then method I now have an array of tickets and every ticket has an type object with the information of the ticket type.❣️

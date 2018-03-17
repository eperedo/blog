---
title: Virtual Attributes in objection.js
date: 2018-03-14 21:10:00
---

I need generate an extra field [for every ticket I have](https://blog.eperedo.com/2018/03/13/objection-js-relationships/). This new field will be a boolean indicating true if the number is 1 and false in any other value.
I can easily add a new field and generate the value according the rule I want but it's kind silly to have an extra field in the table just for this flag.

### Virtual Attributes

[Objection.js](https://vincit.github.io/objection.js) allows me to generate any extra field according the values of a Model. This means I can generate a new field called `isFirst` and it will be generated dinamically by objection.

First I need to use the [virtualAttributes](https://vincit.github.io/objection.js/#virtualattributes) method, it must return an array with the names of the fields I want to add to my results.

```javascript
static get virtualAttributes() {
  return ['isFirst'];
}
```

Now I need to define the `isFirst` as a function

```javascript
get isFirst() {
  return this.number === 1;
}
```

I am writing a new function and inside of it putting a simple condition, if the number is equal to 1 it should return true otherwise false.
The `this` keyword gives me access to the value of each row my query might contains.
It means that if I make a query like this:

```sql
select id, number from ft_tickets where id = 1;
```

This will return one row

```json
{
	"id": 1,
	"number": 1
}
```

In this case the `this.number` will have the value of `1` and objection will add a new field called `isFirst` with the vaue of true.
The same logic applys if the query include many rows.

### Output

If I executed `node queries.js`

```javascript
Ticket.query()
	.eager('type')
	.select('id', 'number')
	.then(tickets => {
		console.log('Is First?', tickets[0].isFirst);
		console.log('Is First?', tickets[1].isFirst);
	});
```

it will return the output:

```javascript
Ticket Types [ TicketType { id: 1, name: 'Customer', code: 'CUS' },
  TicketType { id: 2, name: 'VIP', code: 'VIP' },
  TicketType { id: 3, name: 'Regular', code: 'REG' } ]
Is First? true
Is First? false
```

Awesome like always 🇵🇪

---
title: Insert data in Objection.js
date: 2018-03-16 23:10:00
---

So far the [only thing I was doing](https://blog.eperedo.com/2018/03/16/knex-skip-undefined/) is fetch data from the database, but what about insert data?

### Insert data in ObjectionJs

Like every other ORM this operation is easy to implement, [Objection.js](https://vincit.github.io/objection.js) has a `insert` method which take as a paramater a JS object and will transform this object to a valid **INSERT SQL** statement.

```javascript
Ticket.query()
	.insert({ number: 1, type_id: 1 })
	.then(newRecord => {
		console.log('New Record', newRecord);
	});
```

The **insert** method will return the new object so I can use the new `ID` and make aditional queries with it.

```bash
{ number: 1, type_id: 1, id: 3 }
```

Probably the update method will be so easy as the insert was.

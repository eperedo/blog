---
title: SkipUndefined in objection.js
date: 2018-03-15 22:30:00
---

Ok today I will create a function to [get all the tickets](https://blog.eperedo.com/2018/03/15/objection-virtual-attributes/). This function will take one parameter called **quantity**.
This quantity will include only the tickets whose number is greater than the quantity parameter, but the parameter is optional. This means that if the function does not have the parameter it will get all the tickets without any conditional.
Easy I think, I can use and if and call it a day.

```javascript
function greaterThan(quantity) {
	const query = Ticket.query().select('id', 'number');
	if (quantity) {
		query.where('number', '>', quantity);
	}
	return query;
}
```

Great, let's take a pisco sour at the Bolivar Hotel 🍹. Well not so fast...

### SkipUndefined

[Objection.js](https://vincit.github.io/objection.js) have a built in method to avoid the ugly thing I did above: **skipUndefined**.
My code must be rewritten like this:

```javascript
function greaterThan(quantity) {
	return Ticket.query()
		.select('id', 'number')
		.skipUndefined()
		.where('number', '>', quantity);
}
```

Looks cleaner and can be really powerful if you have many optional parameters 👊

---
title: Knex seeds
date: 2018-03-09 22:05:00
---

[Yesterday](https://blog.eperedo.com/2018/03/09/knex-migrations/) I had created my migrations and everythings is great so far, but I will need some data in my table. So I have two options - well in fact I have many options but whatever - either writing a sql file with some default data and run it every time I need or using the seeds knex gives me.

### Knex Seeds

Knex has a command that can generate data for your tables it is kinda similar to migrations.

```bash
npx knex seed:make type-tickets
```

It will create a **seeds** folder and create the file **type-tickets** inside. After a little modifications this is the result of my first seed.

```javascript
exports.seed = function(knex, Promise) {
	return knex('ft_ticket_types')
		.del()
		.then(() => {
			return knex('ft_ticket_types').insert([
				{ id: 1, name: 'Customer', code: 'CUS' },
				{ id: 2, name: 'VIP', code: 'VIP' },
				{ id: 3, name: 'Regular', code: 'REG' },
			]);
		});
};
```

Seeds functions must return a Promise, always! First I am connecting knex to the table **ft_ticket_types**. Then I delete all the data using the **del()** function. Since **del** returns a Promise I can chain the next action using **then**. Inside of the then I will call the knex function again but now I will call the **insert** method. The only parameter I pass is an array of objects where every object represent the table I had created [yesterday](https://blog.eperedo.com/2018/03/09/knex-migrations/).

### Running seeds

Great, my seed is ready to go now I can run my seed using **seed:run**

```bash
npx knex seed:run
```

Looks like everything is working because the output message was

```bash
Ran 1 seed files
```

If I go to adminer - Yeah I love adminer! ❤️ - I can see my data there.

![knex seeds](https://res.cloudinary.com/drukp4ipu/image/upload/v1520784659/blog/seed-example.png)

I can go to sleep now 😴

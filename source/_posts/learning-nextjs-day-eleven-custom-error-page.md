
---
title: Learning NextJs - Day 11: Custom Error Pages
date: 2019-07-11 21:00:00
---

For this day I want to talk about how NextJs deal with Http Errors. The way to customize not only the design of the error page but also the actual status code sent to the clients.

Let's assume we have a simple application with two pages, one for the list of users and another to see the details page of each user.
That means we have two valid urls in our app.

```bash
/ # root url that show a list of users
/users/[id] # dynamic url that will show the detail of one user
```

Let's see quickly the code of one of our pages:

```jsx
// pages/index.js

import Link from "next/link";

const users = [
  {
    id: 1,
    name: "Eduardo",
    age: 20
  },
  {
    id: 2,
    name: "Cinthya",
    age: 30
  }
];

function Home() {
  return (
    <ul>
      {users.map(user => {
        return (
          <li key={user.id}>
            <Link href={`/users/${user.id}`}>
              <a>{user.name}</a>
            </Link>
          </li>
        );
      })}
    </ul>
  );
}

export default Home;
```

Nothing weird here, just iterating an array of users and show them on the page.
Now let's create the details page where we can show all the information of each user.

```jsx
// pages/users/[id].js
const users = [
  {
    id: 1,
    name: "Eduardo",
    age: 20
  },
  {
    id: 2,
    name: "Cinthya",
    age: 30
  }
];

function Details({ user }) {
  return (
    <div>
      <h1>Name: {user.name}</h1>
      <p>Age: {user.age}</p>
      <p>Id: {user.id}</p>
    </div>
  );
}

function getInitialProps({ query, res }) {
  const { id } = query;
  const user = users.find(u => u.id == id);
  return {
    user: user || {}
  };
}

Details.getInitialProps = getInitialProps;

export default Details;
```

I know repeating the users array is not ideal, but since the goal of this post is NextJs error pages, let's pretend that's ok. This page get the id of the user from the url and then search if that user exist on the **users** array. Then return the result so NextJs can pass the value as a prop to the **Details** component.

Since the app only have two routes if you type any other url in our browser NextJs will show the default Error Page. Let's enter the following url in the browser

```bash
/i-love-tacos
```

NextJs is going to show you this page:

![Default Error Page NextJs](https://thepracticaldev.s3.amazonaws.com/i/ymzf013k9ejitxil465p.png)

And if you open the developer tools you can see the status code of that response is 404 Not Found.

![NextJs Error Page Status Code](https://thepracticaldev.s3.amazonaws.com/i/ozpn2v0x0ik0j0etvwwa.png)

But what about if you type the url:

```bash
/users/3
```

This match the dynamic page: **pages/users/[id].js**, but in the **users** array there is no user with ID equals to 3 so according the current functionality of this page it should return an empty object as a prop to the page.

![Details Page for nonexistent users](https://thepracticaldev.s3.amazonaws.com/i/u28g9fnjvnm5amuazhnp.png)

Instead showing a weird page with incomplete information let's change that and show the default 404 page of NextJs.

```js
// pages/users/[id].js
function getInitialProps({ query }) {
  const { id } = query;
  const user = users.find(u => u.id == id);
  return {
    user: user || {},
    statusCode: user ? 200 : 404
  };
}
```

First we add a new **statusCode** variable to our result object, the value of this variable depends if the user is found in the **users** array. If the user exist the statusCode will be 200, otherwise 404.

But how NextJs could know that we want to show the default Error page? It is quite easy, you need to return the default **error** component.

```jsx
// pages/users/[id].js
import Error from "next/error";

function Details({ user, statusCode }) {
  if (statusCode === 404) {
    // This line will show the default Error Page
    return <Error statusCode={statusCode} />;
  }

  return (
    <div>
      <h1>Name: {user.name}</h1>
      <p>Age: {user.age}</p>
      <p>Id: {user.id}</p>
    </div>
  );
}
```

Pretty easy, right? Now you can see the error page when an nonexistent user is requested. And if you open the developer tools
you can see the 200 status code is returned.
But you are sending the error page! Why is the status code still 200?

![Nonexisting user bad status code](https://thepracticaldev.s3.amazonaws.com/i/td2jkqkkk4oihvpdrhec.png)

Well the response that NextJs is giving you is not responsability of the **Error** component. If you want to show a different status code you need to **set it in your response as well**. Let's do that.

```js
// pages/users/[id].js
function getInitialProps({ query, res }) {
  const { id } = query;
  const user = users.find(u => u.id == id);
  // The find method return undefined if the condition
  // does not match
  if (!user) {
    // here you can assume user is undefined that means
    // it is an nonexistent user so change the status code
    // of the response.
    res.statusCode = 404;
  }
  return {
    user: user || {},
    statusCode: user ? 200 : 404;
  };
}
```

And if you test your page again you will see the error page and the correct status code as well!

![Nonexisting user right status code](https://thepracticaldev.s3.amazonaws.com/i/64cubkxc62lvt2ojkj9z.png)

### Custom Error Page

What about if you do not like the default Error page? Well you can replace it!
In order to do that first you will need a new file called **\_error.js** in your **pages** folder.

```jsx
// pages/_error.js

function CustomError({ statusCode }) {
  return (
    <figure>
      <img
        alt="Showing a properly cat according the status code"
        width="100%"
        src={`https://http.cat/${statusCode}`}
      />
      <figcaption>
        <h1>{statusCode}</h1>
      </figcaption>
    </figure>
  );
}

function getInitialProps({ res, err }) {
  let statusCode;
  // If the res variable is defined it means nextjs
  // is in server side
  if (res) {
    statusCode = res.statusCode;
  } else if (err) {
    // if there is any error in the app it should
    // return the status code from here
    statusCode = err.statusCode;
  } else {
    // Something really bad/weird happen and status code
    // cannot be determined.
    statusCode = null;
  }
  return { statusCode };
}

CustomError.getInitialProps = getInitialProps;

export default CustomError;
```

Now in the details page you need to replace the Error component by the new one:

```jsx
// pages/users/[id].js
import Error from "./_error.js";
```

And if you try again you will see a lovely cat telling you
got a 404 error page.

![Custom Error Page NextJs](https://thepracticaldev.s3.amazonaws.com/i/jy9gthggjc1h4bcs787t.png)

Play with the full code here:
https://github.com/eperedo/learning-nextjs/tree/nextjs-day-11-error

### Things you learned!

- Now you can control how and when show the default error page
- You can modify the status code of your responses to keep an equivalent with the current Error you are displaying.
- You can customize the aspect of the error page

---
title: Learning NextJs - Day 2
date: 2019-06-18 18:00:00
---

Since Next can render your React component in the server all components have an special lifecycle event
called **getInitialProps**.

This function accept asyncronous code so you can easily fetch data from a remote resource, like an http request.

Let's extend our Home page to fetch a list of users from the [jsonplaceholder](https://jsonplaceholder.typicode.com/users) API.

The component will receive a **users** prop with an array of objects, every object with the user information. You can use the map method to iterate over the users and display the name of each one.

```jsx
function Home({ users }) {
  return (
    <div>
      <h1>Users</h1>
      {users.map(user => (
        <p key={user.id}>{user.name}</p>
      ))}
    </div>
  );
}

export default Home;
```

Now you need to pass the users prop and in this moment the **getInitialProps** enter to the stage.

```jsx
function Home({ users }) {
  return (
    <div>
      <h1>Users</h1>
      {users.map(user => (
        <p key={user.id}>{user.name}</p>
      ))}
    </div>
  );
}

async function getInitialProps() {
  const response = await fetch("https://jsonplaceholder.typicode.com/users");
  const users = await response.json();
  return { users };
}

Home.getInitialProps = getInitialProps;

export default Home;
```

You must return a javascript plain object and next will inject as props to the component.
This method is only allowed to use in **pages** components, not a regular ones.
If you try this it is going to throw an error

```bash
ReferenceError: fetch is not defined
```

And this is because the **getInitialProps** is executed on the server, and node does not have
a native **fetch** method like the browser have.
One alternative is using a library like axios, supertest, superagent, etc. But in this case I am going to use
node-fetch, because I do not want to change the above code.

```bash
npm install node-fetch
```

And now you need to add one line of code to the component importing node-fetch

```
import fetch from 'node-fetch';
```

And if you open your browser at http://localhost:3000 you will see a list of users loaded from
an external API over http.

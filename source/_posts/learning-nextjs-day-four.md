---
title: Learning NextJs - Day 4
date: 2019-06-21 09:00:00
---

You can add css to your pages components using [styled-jsx](https://github.com/zeit/styled-jsx) and since the creators of this library are the same than next you can use it without write a single line of configuration.

You will need the special **style jsx** component inside of your pages.

```jsx
function Home({ users }) {
  return (
    <div>
      <h1>Users</h1>
      {users.map(user => (
        <p key={user.id}>{user.name}</p>
      ))}
      <style jsx>{`
        p {
          color: blue;
        }
      `}</style>
    </div>
  );
}

export default Home;
```

This page will show a list of users and the name of each user will be **blue** since we are giving that color to the **p** element.

One advantage of style jsx is that our css is isolated from the rest of the world, in this case **the world** means other pages/components. So other **p** elements will remaing with the default color.

### Global Styles

But what about if we want to all of the **p** elements have the same color? Easy, style jsx give you an special **global** prop.

```jsx
function Home({ users }) {
  return (
    <div>
      <h1>Users</h1>
      {users.map(user => (
        <p key={user.id}>{user.name}</p>
      ))}
      <style global jsx>{`
        p {
          color: blue;
        }
      `}</style>
    </div>
  );
}

export default Home;
```

### Dynamic Styles

Let's suppose your manager goes crazy and tell you that your users pages needs
a button for every user and if the ID of the user is greater than 5 the button background must be **red** otherwise **green**.

First you create a custom button component and you accept and **ID prop** and according to that writing a simple ternary you can make the crazy request.

```jsx
function CustomButton({ children, id }) {
  return (
    <button>
      {children}
      <style jsx>{`
        button {
          background: ${id > 5 ? "red" : "green"};
        }
      `}</style>
    </button>
  );
}
```

Now in your home page just import the component

```jsx
import CustomButtom from "./../components/CustomButtom";

function Home({ users }) {
  return (
    <div>
      <h1>Users</h1>
      {users.map(user => (
        <div key={user.id}>
          <p>{user.name}</p>
          <CustomButtom id={user.id}>Click Me</CustomButtom>
        </div>
      ))}
      <style global jsx>{`
        p {
          color: blue;
        }
      `}</style>
    </div>
  );
}

export default Home;
```

And now you have dynamic styles using the values of props.

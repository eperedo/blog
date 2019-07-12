---
title: Learning NextJs - Day 10
date: 2019-07-11 21:00:00
---

So in the middle of my learning process the team of nextjs decided to make a huge release and I am going to cover 1 of the many really great features that they include in the last version:

### Dynamic Routes

So you probably remember that for dynamic routes in nextjs you normally need to [create a custom server](https://blog.eperedo.com/2019/06/28/learning-nextjs-day-eight/). This can sound like something difficult to do but actually it is really simple.

The nextjs team realized that it is a common task to do in a real web application so they decide to build this feature into the core of nextjs.

Let's create a new nextjs app with 3 routes:

1. A Home page, nothing special here.
```
/
```
2. A users page, it is going to show a list of users

```
/users
```
3. A user profile page to show the information of one user

```
/users/{user-id}
```

There's nothing new in the installation process with this version, same as previous

```bash
npm install next@9.0.0 react react-dom
```

Now let's create our pages folder, and inside of it the following structure to achieve our 3 routes

```bash
pages
  index.js # this is going to generate the home url
  users
    index.js # this is going to generate the users url (/users)
    [id].js # Square brackets means dynamic parameters for nextjs, it is going to # generate the url: /users/{id}
```

Yes, is that easy! If you want to create a dynamic url you need to define the dynamic part inside square brackets, and the name that you put in there is going to be added to the query object in your **getInitialProps** method.

Ok let's create the components for each of our pages. If you are not familiar with nextjs pages you can see [an introduction here](https://blog.eperedo.com/2019/06/16/learning-next-day-one/)

```jsx
// pages/index.js
import Link from 'next/link';

function Home() {
  return (
   <div>
     <h1>Welcome to NextJs 9!</h1>
      <Link href="/users">
        <a>My Users</a>
      </Link>
   </div>
  );
}

export default Home;
```

Nothing new here, just a welcome message an a Link to the users page  

```jsx
import Link from 'next/link';

const users = [
  {
    id: 1,
    name: 'Eduardo',
  },
  {
    id: 2,
    name: 'Cinthya',
  },
];

function Users() {
  return (
    <ul>
      {users.map(user => {
        return (
          <li key={user.id}>
            <Link href={`/users/${user.id}`}>
              <a>
                {user.name}
              </a>
            </Link>
          </li>
        );
      })}
    </ul>
  );
}

export default Users;
```

I am using an array of users to make code shorter, but you totally can [fetch data from an API](https://blog.eperedo.com/2019/06/18/learning-next-day-two/) or wherever you want.

And now our dynamic page

```jsx
function UserProfile({ user }) {
  return (
    <h1>
      Hello! My ID is {user.id}
    </h1>
  );
}

async function getInitialProps({ query }) {
  // Since our file name is [id].js
  // nextjs is going to add the value of
  const { id } = query;
  return {
    user: {
      id,
    },
  };
}

UserProfile.getInitialProps = getInitialProps;

export default UserProfile;
```

The implementation is the same as having a custom server, so kudos to nextjs team to adding great features without major changes!

### Multiple dynamic parameters

Ok but what about if you want the dynamic profile to be a little more complex, like instead

```
/users/{id}
```

you want this:

```
/users/{id}/{name}/profile
```

Not a challenge for nextjs since the new file system API supports multiple parameters in folders as well.

You can create the above route using this folder structure

```
pages
  users
    [id]
      [name]
        profile.js
```

now our **query** object will have two properties: **id** and **name**

```jsx
// /users/[id]/[name]/profile.js

function UserProfile({ user }) {
  return (
    <h1>
      Hello! My ID is {user.id}, but you can call me {user.name}
    </h1>
  );
}

async function getInitialProps({ query }) {
  const { id, name } = query;
  return {
    user: {
      id,
      name,
    },
  };
}

UserProfile.getInitialProps = getInitialProps;

export default UserProfile;
```

And of course to get a fully example we need to change the **href** prop in our users page as well

```jsx
// users/index.js
import Link from 'next/link';

const users = [
  {
    id: 1,
    name: 'Eduardo',
  },
  {
    id: 2,
    name: 'Cinthya',
  },
];

function Users() {
  return (
    <ul>
      {users.map(user => {
        return (
          <li key={user.id}>
            <Link href={`/users/${user.id}/{user.name}/profile`}>
              <a>
                {user.name}
              </a>
            </Link>
          </li>
        );
      })}
    </ul>
  );
}

export default Users;
```

### Things We learned

1. You can create dynamic routes using the File System API.

2. You can apply multiple params for a dynamic route using square brackets in folders and files.

3. NextJs is awesome!

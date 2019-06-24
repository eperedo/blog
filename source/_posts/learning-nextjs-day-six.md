---
title: Learning NextJs - Day 6
date: 2019-06-24 17:53:00
---

URL's are a key part of every web application, let's see how we can pass data between different pages.

Let's start adding a Link to our users list, this link will redirect to the about page, but passing the id of the selected user.
We will use that ID to get the information about the current user and display it in the page. Also we will use that information to generate different meta tags per user.

To accomplish that we will use the Link component.

```jsx
import Link from "next/link";

function Home({ users }) {
  return (
    <div>
      <Head>
        <title>Home Page</title>
      </Head>
      <h1>Users</h1>
      {users.map(user => (
        <div key={user.id}>
          <p>{user.name}</p>
          {/* here we pass the user id in the query string */}
          <Link prefetch href={`/about?userId=${user.id}`}>
            <a>About</a>
          </Link>
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
```

If you go to the root url you can see an About link per each user. If you click on that it will take you to the about page along with the user id.

Now let's actually take that user id and get the information about that specific user. We need to use the getInitialProps method. One of the values available in this function is the **query** object. It will have all the values that you pass in the url as a query string. In this case **query** is an object with a property **userId** and with the value of the selected user.

Something like this:

```js
query = {
  userId: 1 // The value is dynamic depending of the user selected
  // in the home page.
};
```

With the user id we can use the jsonplaceholder API to get the rest of the user information.

```jsx
async function getInitialProps({ query }) {
  const response = await fetch(
    `https://jsonplaceholder.typicode.com/users/${query.userId}`
  );
  const user = await response.json();
  // This method always needs to return an plain JS object.
  // The object is going to be the props of our page component.
  return { user };
}

About.getInitialProps = getInitialProps;
```

Now you have a user prop in the **about** page. Let's use that information to generate custom meta tags per each user.

```jsx
import Head from "next/head";
import fetch from "node-fetch";

function About({ user }) {
  return (
    <div>
      <h1>Welcome to the About Page!</h1>
      <p>
        The author of this page is {user.name}, check meta tags in the view
        source!
      </p>
      <Head>
        <title>{user.name} - About Page</title>

        {/* HTML meta tags */}

        <meta
          name="description"
          content="Hey google, bing, altavista, etc this is my about page, please rank me nice!"
        />
        <meta name="robots" content="index, follow" />
        {/* This is going to render a different author meta tag per each user */}
        <meta name="author" content={user.name} />

        {/* Twitter meta tags */}
        <meta name="twitter:card" content="summary" />
        <meta name="twitter:site" content="@eperedo" />
        <meta name="twitter:title" content="Learning NextJs" />
        <meta
          name="twitter:description"
          content="Hey twitter users! This is my about page built with NextJs!"
        />

        {/* Facebook meta tags */}
        <meta property="og:type" content="article" />
        <meta
          property="og:title"
          content="Hello Facebook! This is my about page built with NextJs!"
        />
        <meta property="og:site_name" content="Learning NextJs" />
        <meta property="og:url" content="http://localhost:3000" />
        <meta
          property="og:image"
          content="https://picsum.photos/id/607/200/300"
        />
        <meta property="article:published_time" content="2019-06-22" />
        <meta
          property="article:author"
          content="https://facebook.com/my-profile"
        />
      </Head>
    </div>
  );
}

async function getInitialProps({ query }) {
  //
  const response = await fetch(
    `https://jsonplaceholder.typicode.com/users/${query.userId}`
  );
  const user = await response.json();
  return { user };
}

About.getInitialProps = getInitialProps;

export default About;
```

Now go to the root url, click in the About link of any user and you will see a custom about page per user. If you check the view source of the about page you can see that the value of the author meta tag is the name of the user!

Ok ok I know what you are thinking. Passing data in query strings is so 90's! We can totally have clean urls with next, but that's information for the day 7 I guess.

NextJs gives you without any efforts a clean 404 error page. Let's start the server and put in the address bar a non existing url like this:

```bash
/this-is-a-fake-url
```

If you try to open that url you will get a nice and minimalist 404 error page.

![Default 404 error in NextJs](https://thepracticaldev.s3.amazonaws.com/i/9us9wnydtjvtptywbqjk.png)

We have an about page that render some user information from the [jsonplaceholder ](https://jsonplaceholder.typicode.com) API. But we are not controlling the scenario where this API throws an 404 error.
Normally this API only have 10 users, so if you pass the ID of 11 to the **/users/11** endpoint it will throw a 404 status code.
But even in this case We just get a blank page and not the error page we want.

Let's solve this, first you will need to verify the status code of the response in the **getInitialProps** method

```jsx

async function getInitialProps({ query }) {
  const response = await fetch(
    `https://jsonplaceholder.typicode.com/users/${query.userId}`,
  );
  const user = await response.json();
  // We add the status code to our props
  return { user, statusCode: response.status };
}

```

We are just adding a new prop called **statusCode** and with this precious information you can add a simple validation in your component and return the default next error page.

```jsx
import Error from 'next/error';

function About({ user, statusCode }) {
  // render the nextjs error component
  // if the statusCode prop has the 404 value
  if (statusCode === 404) {
    return <Error statusCode={statusCode} />;
  }
  // rest of the code
  return (
    ...
  );
}

```

If you start the server and go to the url

```bash
/users/1000/about
```

You get the 404 error page.


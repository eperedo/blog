---
title: Learning NextJs - Day 5
date: 2019-06-22 14:00:00
---

One of the benefits of render our pages on server side is the ability to get the full control of the whole html that the browser will receive.

Normally there is a section of the HTML that you want to be different for all the pages: the **head** section.

Next have this special component **Head**. It will append all the content you put inside of it to the head html tag once your page is rendered.

```jsx
import Head from "next/head";

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
```

Now your home page will include in the html a title tag with the value "Home Page", you actually can verify it using the **view source** option of the browser.
And you can have different values for each of your pages.

```jsx
import Head from "next/head";

function About() {
  return (
    <div>
      <h1>Welcome to the About Page!</h1>
      <Head>
        <title>About Page</title>
      </Head>
    </div>
  );
}

export default About;
```

### SEO

Since you can add any valid html inside head now you can improve the SEO of your pages. Let's add meta tags to give google, facebook and twitter robots more information about our web.

```jsx
function About() {
  return (
    <div>
      <h1>Welcome to the About Page!</h1>
      <Head>
        <title>About Page</title>
        {/* HTML meta tags */}

        <meta
          name="description"
          content="Hey google, bing, altavista, etc this is my about page, please rank me nice!"
        />
        <meta name="robots" content="index, follow" />
        <meta name="author" content="Eduardo P. Rivero" />

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

export default About;
```

And of course you can do the same for all the pages of your website.

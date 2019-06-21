---
title: Learning NextJs - Day 3
date: 2019-06-20 18:00:00
---

Now that you can [create your own pages](https://blog.eperedo.com/2019/06/16/learning-next-day-one/) in next you certainly will need a way
to navigate between them without writing the url directly in your browser.

For that purpose the next framework has built in a component called **Link**.
Let's add in our home page two links one for the root page and another for
the **About** page.

```jsx
import Link from "next/link";

function Home() {
  return (
    <div>
      <Link href="/about">
        <a>Home</a>
      </Link>
      <Link href="/about">
        <a>Home</a>
      </Link>

      <h1>Welcome to my App!</h1>
    </div>
  );
}
```

Note that the **Link** component is just a wrapper which provide you client side transitions between your pages, that's why I am putting an **"a"** tag inside of it.
If you open your browser at http://localhost:3000 you can easily navigate between them.

### Prefetching

You can pass the **prefetch** prop to the **Link** component. If next see this prop it will... well prefetch your page.

```jsx
import Link from "next/link";

function Home() {
  return (
    <div>
      <Link href="/about">Home</Link>
      <Link prefetch href="/about">
        About
      </Link>

      <h1>Welcome to my App!</h1>
    </div>
  );
}
```

In the above code next will donwload all the javascript code related to the about page in a background process making the interaction with that page faster. This is a **production only** feature so you can only test it once your app is deployed.

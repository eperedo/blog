---
title: Learning NextJs - Day 7
date: 2019-06-25 17:56:00
---

You [already now how to link and pass data between your nextjs pages](https://blog.eperedo.com/2019/06/24/learning-nextjs-day-six/). But let's face it, querystring are not cool! So for this day I will update that part of the code to turn our boring url to a clean one.

It is a pretty easy task since the **Link** component comes with a **"as"** prop that allow you to define an alias for your url.

```jsx
import Link from "next/link";

function Home({ user }) {
  // Other part of code omitted for reading purposes
  return (
    <Link href={`/about?userId=${user.id}`} as={`/users/${user.id}/about`} />
  );
}
```

In the above code we are creating a Link component, its real route is **/about?userId=\${user.id}**, but with the **as** prop you are telling to nextjs that you want to show that url in your address.

If you start the server and click on one of the users you will see that the new url in your browser is something like **/users/1/about" instead **/about?userId=1\*\*

But like uncle Ben said: **A great feature carries great unintended behaviors** if you select one of the users then reload the page next will throw a 404 error page and that's correct since **/users/1/about** is just an alias, it actually does not exist on the server.

So I guess day 8 is going to be a really fun day. Stay tuned!

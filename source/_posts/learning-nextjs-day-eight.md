---
title: Learning NextJs - Day 8
date: 2019-06-27 19:00:00
---

You can [easily add alias to your pages](https://blog.eperedo.com/2019/06/25/learning-nextjs-day-seven/), but now the 404 problem happen every time you refresh the page.
In order to solve this you need to create a custom server, I know! sounds scary, but actually is not because nextjs is a node web server.

This means integrate it with express, hapijs, fastify, or any other node server is really easy. They are plenty of examples how to do this in [their docs](https://nextjs.org/docs#custom-server-and-routing).

I am going to use HapiJs because I love it so much.

### Creating a HapiJs Server

First thing is install hapijs as a dependency

```bash
npm install @hapi/hapi
```

Now you need to create a file where the integration between next and hapi is happen.

```bash
touch server.js # this create a new file server.js
```

First you need to create a simple hapijs server.

```js
const Hapi = require("@hapi/hapi");

const server = Hapi.Server({
  host: process.env.HOST || "localhost",
  port: process.env.PORT || 3000
});

(async () => {
  await server.start();
  console.log(`Server is running at ${server.info.uri}`);
})();
```

Remember this is server side and more specific nodejs land, so you cannot use fancy **import** syntax yet. That's because I am using **require**.

After the require we create a hapi server using the **Hapi.Server** method, you can pass many options to it, but for now the host and the port are enough. Both have default values to **localhost:3000** but you can change it using environment variables. Then inside a IIFE we call the start method which is a promise so we can use await keyword and after that print a nice message to the terminal with the server information.

### Integrating NextJs and HapiJs

Since NextJs is another node web server, create it is almost the same than hapijs. First a require, then a method that return a server instance and finally the function to get access to nextjs internals.

```js
// The Require!
const next = require("next");
const Hapi = require("@hapi/hapi");

// the method that return a nextjs instance
// the dev property will start next in dev mode
// in production you want to pass it a falsy value.
const app = next({ dev: true });

const server = Hapi.Server({
  host: process.env.HOST || "localhost",
  port: process.env.PORT || 3000
});

(async () => {
  // We need to wait to next to be ready
  // then start the hapi server.
  await app.prepare();

  await server.start();
  console.log(`Server is running at ${server.info.uri}`);
})();
```

Cool, but our HapiJs server does not know a thing about how to render react components. How we can give this ability to our HapiJs server? Well the nextjs team is one step ahead and have created a helper file to make the integration even easier.

You can [download it here](https://github.com/zeit/next.js/blob/master/examples/custom-server-hapi/next-wrapper.js).
We will add it to the project under the name **nextWrapper.js**, require it and create a couple of new routes.

Right now we have 2 files in the **pages** folder: Index and About. But we can potentially have a lot more, so How we can tell Hapi to get all the files inside this folder, render the component and return the html to the client? We are going to create a generic route that basically will match any possible request to our hapijs server and process this route with one of the methods inside the **nextWrapper.js** file.

```js
// other lines of code omitted
// for better reading purposes
const { defaultHandlerWrapper } = require("./nextWrapper");

(async () => {
  await app.prepare();

  // We use regex to tell hapi to catch
  // all the request and process them using the
  // defaultHandlerWrapper method.
  server.route({
    method: "GET",
    path: "/{p*}",
    handler: defaultHandlerWrapper(app)
  });

  await server.start();
  console.log(`Server is running at ${server.info.uri}`);
})();
```

The **defaultHandlerWrapper** method is the responsible for process all the request to our hapijs server and make the magic that by default nextjs does.

Nextjs create a lot of stuff behind the courtains on a **\_next** folder. That means we need another route in our hapijs server. For this case we are going to use the **nextHandlerWrapper** method of the **nextWrapper.js** file.

```js
// other lines of code omitted
// for better reading purposes
const { defaultHandlerWrapper, nextHandlerWrapper } = require("./nextWrapper");

(async () => {
  await app.prepare();

  server.route({
    method: "GET",
    path: "/_next/{p*}",
    handler: nextHandlerWrapper(app)
  });

  server.route({
    method: "GET",
    path: "/{p*}",
    handler: defaultHandlerWrapper(app)
  });

  await server.start();
  console.log(`Server is running at ${server.info.uri}`);
})();
```

Now we cannot longer use the **dev** script because that is going to open the next server and we want to
start our new hapijs server integrated with next so let's change that script.

```json
{
  "scripts": {
    "dev": "node server.js"
  }
}
```

And if you type in your terminal **npm run dev** everything is working the same! Yep you still get that 404 error in the about page.
Let's fix that creating a route in our hapijs server, in this case the route needs this format:

```bash
/users/{userId}/about
```

And every time this url get a request it should point to the file **about.js** inside the **pages** folder
and do the next magic (read the component on the server, render it and return the response to the client).
It is pretty similar to the previous routes, but now we are use a third method inside the **nextWrapper.js** file: **pathWrapper**.

```js
// other lines of code omitted
// for better reading purposes
const {
  defaultHandlerWrapper,
  nextHandlerWrapper,
  pathWrapper
} = require("./nextWrapper");

(async () => {
  await app.prepare();

  server.route({
    method: "GET",
    // this path is the same we put in the "as" prop
    // of the Link Component
    path: "/users/{userId}/about",
    // This method get the request from the client and return
    // the result of the about.js file
    handler: pathWrapper(app, "/about")
  });

  server.route({
    method: "GET",
    path: "/_next/{p*}",
    handler: nextHandlerWrapper(app)
  });

  server.route({
    method: "GET",
    path: "/{p*}",
    handler: defaultHandlerWrapper(app)
  });

  await server.start();
  console.log(`Server is running at ${server.info.uri}`);
})();
```

Ok we need to start our server again

```bash
npm run dev
```

And if you select an user you still see your nice url "/users/{id}/about" in your browser but if you refresh
that page you do not get a 404 page anymore!

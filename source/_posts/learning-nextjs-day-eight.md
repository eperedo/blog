---
title: Learning NextJs - Day 8
date: 2019-06-26 21:00:00
---

You can easily add alias to your url pages, but now the 404 problem happen every time you refresh the page.
In order to solve this you need to create a custom server, I know! sounds scary, but actually is not because nextjs is a node web server.

This means integrate it with express, hapijs, fastify, etc is really easy. They plenty of example how to do this in [their docs](https://nextjs.org/docs#custom-server-and-routing).

I am going to use HapiJs because I love it so much.

### Creating a HapiJs Server

First thing is install hapijs as a dependency

```bash
npm install @hapi/hapi
```

Now you need to create a file where the integration between next and hapi is going to live.

```bash
touch server.js # this create a new file server.js
```

First you need to create a simple hapijs server.

```js
const Hapi = require("@hapi/hapi");

const server = Hapi.Server({
  HOST: process.env.HOST || "localhost",
  PORT: process.env.PORT || 3000
});

(async () => {
  server.route({
    handler: () => "Hello World",
    method: "/GET",
    path: "/"
  });

  await server.start();
  console.log(`Server is running at ${server.info.uri}`);
})();
```

Remember this is server side and more specific nodejs land, so you cannot use fancy import syntax yet. That's because I am using **require**.

After the require we create a hapi server using the **Hapi.Server** method, you can pass many options to it, but for now the host and the port are enough. Both have default values to **localhost:3000** but you can change it using environment variables. Then inside a IIFE we call the start method which is a promise so we can use await keyword and after that print a nice message to the terminal with the server information.

### Integrating NextJs and HapiJs

Since NextJs is another node web server, creating is almost the same than hapijs. First a require, then a method that return a server instance and finally the function to start the server.

```js
// The Require!
const next = require("next");
const Hapi = require("@hapi/hapi");

// the method that return a server instance
// the dev property will start next in dev mode
// in production you want to pass it a falsy value.
const app = next({ dev: true });

const server = Hapi.Server({
  HOST: process.env.HOST || "localhost",
  PORT: process.env.PORT || 3000
});

(async () => {
  // We need to wait to next to be ready
  // then start the hapi server.
  await app.prepare();

  server.route({
    handler: () => "Hello World",
    method: "/GET",
    path: "/"
  });

  await server.start();
  console.log(`Server is running at ${server.info.uri}`);
})();
```

Cool, but our HapiJs server does not know a thing about how to render react components. How we can give this ability to our HapiJs server? Well the nextjs team is one step ahead and have created a helper file to make the integration even easier.

You can [download it here](https://github.com/zeit/next.js/blob/master/examples/custom-server-hapi/next-wrapper.js).
We will add it to the project under the name **nextWrapper.js**, require it and create a couple of new routes.

Right now we have 2 files in the **pages** folder: Index and About. But we can potentially have a lot more, so How we can tell Hapi to map all the files inside this folder, render the component and return the html to the client? We are going to create a generic route that basically will match any possible request to our hapijs server and process this route with one of the methods inside the **nextWrapper.js** file.

```js
// other lines of code omitted
// for better reading purposes
const { defaultHandlerWrapper } = require("./nextWrapper");

server.route({
  method: "GET",
  path: "/{p*}" /* catch all route */,
  handler: defaultHandlerWrapper(app)
});
```

---
title: Learning NextJs - Day 9
date: 2019-06-29 17:51:00
---

NextJs by default allows you to bring your own static files (js, css, images, json, etc) without any extra configuration.
In order to do that you need an **"static"** folder inside your project.

So let's create the folder with that specific name

```bash
mkdir static
```

Now you can put an image there (or any other static file) and use it in your pages. Let's pretend you have the logo of your app inside the **static** under the creative name of **mylogo.png**.

You can use the following path to show the logo in an img tag

```bash
/static/mylogo.png
```

Let's create a **contact.js** page

```jsx
function Contact() {
  return (
    <img src="/static/mylogo.png" alt="My App Name" />
  );
}

export default Contact
```

And if you start your NextJs server and go to the url **/contact** you will see your logo.

### Custom Server

If you have integrated next with express, [hapijs](https://blog.eperedo.com/2019/06/28/learning-nextjs-day-eight/) or any other web framework, you will need to declare that route on your web framework as well.

If you are using hapijs the route will look like this

```js
// other code omited 
// for reading purposes

server.route({
  method: 'GET',
  path: '/static/{p*}',
  handler: nextHandlerWrapper(app),
});
```

With this you are telling HapiJs to use the nextHandlerWrapper every time a request to the **static** folder is made.


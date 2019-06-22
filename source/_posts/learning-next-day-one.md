---
title: Learning NextJs - Day 1
date: 2019-06-16 15:00:00
---

In order to get started with next you need to install the latest version of three libraries:

1. Next
2. React
3. React-Dom

```bash
npm install next react react-dom
```

Once the installation is finished you will need a package.json file. You can easily create one
using the npm init command

```bash
npm init -f
```

The **-f** flag means it will use the defaults, you can ommit it and use the values you want. Now
in the **package.json** in the **scripts** section create a **dev** (it can be any name though) script with the next command.

```bash
	"scripts": {
		"dev": "next dev"
	}
```

The **next dev** will generate a webserver for local development powered by next.

### File System API

Next will search for a pages directory and convert all the files that find on there on routes of your
application.

Let's suppose you want an About page url (/about) to display a welcome message. First you need to create a **pages**
directory

```bash
mkdir pages
```

Now since you want the url to be **/about** the file must have the same name **About.js**. Since here
you are in a react safe space or in other words you just need to create a react component.

```jsx
// pages/About.js
function About() {
  return <h1>Welcome to the About Page!</h1>;
}

export default About;
```

Then in your terminal just execute the **dev** script

```bash
npm run dev
```

And that's it, you open in your browser the url http://localhost:3000/about you should see the welcome message.

But what about the root url (/) how we can create content for that page? Easy, in this case next will map the file **index.js** to the root url, clever, eh?

```jsx
// pages/Index.js

function Home() {
  return <h1>Welcome to my App!</h1>;
}

export default Home;
```

Now going to http://localhost:3000 will display the **Welcome to my App** message.

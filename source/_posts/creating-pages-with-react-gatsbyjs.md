---
title: Creating Pages in GatsbyJs
date: 2019-07-06 10:00:00
---

Now that you can [create a new gatsby project](https://blog.eperedo.com/2019/07/05/building-websites-with-react-gatsbyjs/)
let's continues with a fundamental feature that gatsby has built in: **Pages**

A gatsby page is just a React component, but thanks to gatsby it will generate a page in our website. This means that if you create a page
called "users.js" automatically gatsby will call that file when you go to the url **/users** in your development server.

### Creating Gatsby Pages

If you open the project that we create in the previous post you will see a folder structure like this

```bash
/src
  /pages
    index.js
/static
...
# other files
```

In a first look is easy to assume that every time you need a new route/url in your website you need to create
a react component inside the **pages** folder.

If you open the **index.js** you can see a simple React component, actually a FUNCTION component.

```jsx
import React from "react";

export default () => <div>Hello world!</div>;
```

I love arrow functions, but not this much so I will rewrite this component to something more fun (at least to me!)

```jsx
import React from "react";

function Index() {
  return <div>Hello world!</div>;
}

export default Index;
```

Ok now my brain feels better.

Let's create a website that will have two pages:

```bash
/ # root of the project it will
/customers # a page that will show information about our customers
```

Since the root page is already created, let's create the customers page.

```jsx
import React from "react";

const customers = [
  {
    id: 1,
    email: "george.bluth@reqres.in",
    first_name: "George",
    last_name: "Bluth",
    avatar: "https://s3.amazonaws.com/uifaces/faces/twitter/calebogden/128.jpg"
  },
  {
    id: 2,
    email: "janet.weaver@reqres.in",
    first_name: "Janet",
    last_name: "Weaver",
    avatar: "https://s3.amazonaws.com/uifaces/faces/twitter/josephstein/128.jpg"
  },
  {
    id: 3,
    email: "emma.wong@reqres.in",
    first_name: "Emma",
    last_name: "Wong",
    avatar:
      "https://s3.amazonaws.com/uifaces/faces/twitter/olegpogodaev/128.jpg"
  }
];

function Customers() {
  return customers.map(customer => {
    return (
      <section key={customer.id}>
        <img src={customer.avatar} alt={customer.first_name} />
        <div>
          <h2>
            {customer.first_name} {customer.last_name}
          </h2>
          <p>{customer.email}</p>
        </div>
      </section>
    );
  });
}

export default Customers;
```

Let's go through this component, first we create an array called **data**, this array will contain the information about 3 people.
The data was taken from the [reqres.in API](https://reqres.in/api/users) since we do not want to complicate things by involving async requests
(yet!).
After that we define a simple react component that will loop through the array and show the name, email and avatar of each customer.
Ok with this information gatsby automatically will generate a **/customers** url for us. You do not need to restart the server, just go to
your browser and open the following url:

```bash
http://localhost:8000/customers
```

You should see something like this:

![Gatsby Pages](https://thepracticaldev.s3.amazonaws.com/i/nca8memmjo7rkf5xjgfl.PNG)

Ok pretty cool, but we need a good way to navigate between the home/customer pages, let's see how we can achieve that.

### Navigating between Gastby pages

Gatsby has a special component called **Link** and it's pretty useful to navigate between the pages without reload the whole page.
Let's create a navigation section for our website, first in our index page.

```jsx
import React from "react";
// Special Gatsby Component
import { Link } from "gatsby";

function Index() {
  return (
    <div>
      <nav>
        <ul>
          <li>
            <Link to="/">Home</Link>
          </li>
          <li>
            <Link to="/customers">Customers</Link>
          </li>
        </ul>
      </nav>
      <h1>Welcome to my Website</h1>
    </div>
  );
}

export default Index;
```

If you have used [react-router](https://reacttraining.com/react-router/web/guides/quick-start) before it's pratically the same component.
It has a **to** prop and you need to pass the url there.

Ok now copy the same code in the **customers** page.

```jsx
import React from "react";
import { Link } from "gatsby";

const data = [
  {
    id: 1,
    email: "george.bluth@reqres.in",
    first_name: "George",
    last_name: "Bluth",
    avatar: "https://s3.amazonaws.com/uifaces/faces/twitter/calebogden/128.jpg"
  },
  {
    id: 2,
    email: "janet.weaver@reqres.in",
    first_name: "Janet",
    last_name: "Weaver",
    avatar: "https://s3.amazonaws.com/uifaces/faces/twitter/josephstein/128.jpg"
  },
  {
    id: 3,
    email: "emma.wong@reqres.in",
    first_name: "Emma",
    last_name: "Wong",
    avatar:
      "https://s3.amazonaws.com/uifaces/faces/twitter/olegpogodaev/128.jpg"
  }
];

function Customers() {
  return (
    <section>
      <nav>
        <ul>
          <li>
            <Link to="/">Home</Link>
          </li>
          <li>
            <Link to="/customers">Customers</Link>
          </li>
        </ul>
      </nav>
      {data.map(customer => {
        return (
          <section key={customer.id}>
            <img src={customer.avatar} alt={customer.first_name} />
            <div>
              <h2>
                {customer.first_name} {customer.last_name}
              </h2>
              <p>{customer.email}</p>
            </div>
          </section>
        );
      })}
    </section>
  );
}

export default Customers;
```

Now the website has a easy way to navigate between the pages

![Gatsby pages navigation link home](https://thepracticaldev.s3.amazonaws.com/i/v6bs5brvit46nf45q6ju.PNG)
![Gatsby pages navigation link customers](https://thepracticaldev.s3.amazonaws.com/i/d5webwjz94ryrj58tmhb.PNG)

but repeating the same code in both pages does not feel very good and probably against the react philosophy. So let's extract the navigation
in a react component.

### Creating and using React components in Gatsby

Since Gatsby use React under the hoods we can create our UI in tiny reusable components. Let's create a component called **TheNavBar**.
Since we do not want to gatsby turn this component into a page, let's create a folder called **components** and inside of it the navbar.

Our folder structure will look like this:

```bash
src
  /components
    TheNavBar.js
  /pages
```

And our component:

```jsx
import React from "react";
import { Link } from "gatsby";

function NavBar() {
  return (
    <nav>
      <ul>
        <li>
          <Link to="/">Home</Link>
        </li>
        <li>
          <Link to="/customers">Customers</Link>
        </li>
      </ul>
    </nav>
  );
}

export default NavBar;
```

How we can reuse this component inside our pages? Just importing like we normally do in a react application. First our home page

```jsx
import React from "react";
import NavBar from "./../components/TheNavBar";

function Index() {
  return (
    <div>
      <NavBar />
      <h1>Welcome to my Website</h1>
    </div>
  );
}

export default Index;
```

And now the **customers** page

```jsx
import React from "react";
import NavBar from "./../components/TheNavBar";

const data = [
  {
    id: 1,
    email: "george.bluth@reqres.in",
    first_name: "George",
    last_name: "Bluth",
    avatar: "https://s3.amazonaws.com/uifaces/faces/twitter/calebogden/128.jpg"
  },
  {
    id: 2,
    email: "janet.weaver@reqres.in",
    first_name: "Janet",
    last_name: "Weaver",
    avatar: "https://s3.amazonaws.com/uifaces/faces/twitter/josephstein/128.jpg"
  },
  {
    id: 3,
    email: "emma.wong@reqres.in",
    first_name: "Emma",
    last_name: "Wong",
    avatar:
      "https://s3.amazonaws.com/uifaces/faces/twitter/olegpogodaev/128.jpg"
  }
];

function Customers() {
  return (
    <section>
      <NavBar />
      {data.map(customer => {
        return (
          <section key={customer.id}>
            <img src={customer.avatar} alt={customer.first_name} />
            <div>
              <h2>
                {customer.first_name} {customer.last_name}
              </h2>
              <p>{customer.email}</p>
            </div>
          </section>
        );
      })}
    </section>
  );
}

export default Customers;
```

Much cleaner and if you open your browser you will not see any change because we just did a small refactor, the functionality is the same!

### Things that we learn

1. In this post we learned how gatsby turn our react components into powerfull pages.
2. How to create a navigation bar using the Link components.
3. How to reuse react components inside gatsby pages.

[Github Repo](https://github.com/eperedo/learning-gatsby/tree/gatsby-pages)

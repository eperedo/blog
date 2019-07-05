---
title: Building Websites with React and GatsbyJs
date: 2018-07-04 21:38:00
---

Creating a gatsby project is easy since you can use their wonderful CLI.

### Gatsby CLI

In order to install the CLI you will need nodejs and npm/yarn on your computer. If you are a npm person you will need to type the following in your terminal

```bash
npm install -g gatsby-cli
```

And if you are a bit dissapointed [about npm because the way they are treating their employees](https://www.theregister.co.uk/2019/04/01/npm_layoff_staff/) and using yarn instead type this:

```bash
yarn add global gatsby-cli
```

Once the installation has finished you can check if the gatsby cli is working ok with the following command

```bash
gatsby --version
Gatsby CLI version: 2.7.7
```

### Creating a new Gatsby Project

Now you can create a new gatsby project using the **new** command, this command takes 2 parameters, the first one is the name of your project and the second one is the template that will used as a base for the initial files.

```bash
gatsby new learning-gatsby https://github.com/gatsbyjs/gatsby-starter-hello-world
```

with the above command you are telling gatsby: "Hey, please create a new project that I would love to call "learning-gatsby" and please use the hello-world template". According the documentation it is a good template if you are just starting with gatsbyjs.

This step can take a longer time since it will clone the project and install automatically all the project's dependencies.

Once the whole process is done you can enter to the project folder and start the development server to see how the website looks.

### Starting the Development Server

The hello-world template already comes with a script to start the development server, the name of the script is **develop**.
That means you can start a gatsby development server typing in the terminal

```bash
npm run develop
```

and if you are using yarn

```bash
yarn develop
```

This is going to do a lot of work but once you see in your terminal a message like this

```bash
You can now view gatsby-starter-hello-world in the browser.
http://localhost:8000/
```

you can open your browser and go to [http://localhost:8000/](http://localhost:8000/) and you will see the hello world.

![Gatsby Hello World](https://thepracticaldev.s3.amazonaws.com/i/vny7ezsnd0y9lfuvkzrh.png)

Also you can see the graphiql UI in [http://localhost:8000/\_\_\_graphql](http://localhost:8000/___graphql).

![Gatsby GraphQL UI](https://thepracticaldev.s3.amazonaws.com/i/bir8bixcopc04ekwq9la.png)

Oh yeah one of the reasons gatsby is so cool is graphql, but that's information for the next post.

---
title: Creating a custom panel in Directus with ChartJs
date: 2023-02-14 17:00:00
---

When you're working with Directus you have this mini BI tool called [Insights](https://docs.directus.io/app/insights.html).
According the directus docs:

> Insights is a Module in Directus that allows rapid, no-code build-out of analytics dashboards. Regardless of your use-case (be it business intelligence, user progress tracking, web traffic conversions, or whatever!) you will be able to setup key data metrics in just a few clicks.

And every Insight can have many [panels](https://docs.directus.io/app/insights.html#panels-overview). Panels are actually the ones that contains all the visualizations likes: labels, metrics, lists, etc.

I was working on a project where I needed a pie chart. Sadly for me that kind of graphic is not included as a panel in directus by default. The other bad news is that for this particular project I must work with chartjs.

Don't get me wrong here, chartjs does the job very well, but the directus team uses [ApexChartJs](https://apexcharts.com/) for visualizations so I was hoping to reuse that library instead adding a new one.

Lucky for me directus allow you to create a custom panel, so the first thing to do of course is to setup a new directus environment.

## Creating a fresh new directus project

This is an easy step since [there's an official docker container](https://hub.docker.com/r/directus/directus). The only thing you need is create anywhere on your machine the following docker-compose config file.

```yaml
# docker-compose.yaml
version: "3"
services:
  directus:
    image: directus/directus:9.22
    ports:
      - 8055:8055
    volumes:
      - ./database:/directus/database
    environment:
      KEY: "955d861b-5ea1-5996-9aa3-922530ec40cf"
      SECRET: "9916487b-cda1-52c2-b5b5-c8022c45e233"

      ADMIN_EMAIL: "admin@example.com"
      ADMIN_PASSWORD: "123456"

      DB_CLIENT: "sqlite3"
      DB_FILENAME: "./database/data.db"
```

Here we're just pulling the version 9.22 of directus, using sqlite as a database ([see supported databases in case you want a different one](https://docs.directus.io/self-hosted/docker-guide.html#supported-databases)) and using **"admin@example.com"** as an initial user with password **"123456"**.

Now we can run this container

```bash
docker-compose --project-name chartjs up -d
```

After waiting a few seconds you can see the directus login page at http://localhost:8055.

![directus-extension cli questions](https://user-images.githubusercontent.com/461124/218868393-a73d2510-be9c-46de-8d06-8f74f937ae11.PNG)

## Creating a custom panel

In order to create a custom panel we can use the [directus-extension](https://docs.directus.io/extensions/creating-extensions.html) package that is going to generate all the necessary stuff so we can focus in just writing the code related to the panel we want to add, in this case a piechart from chartjs.

```
npm init directus-extension
```

This package is going to ask you 3 questions: extension type (panel in this case), name of the extension (piechart-panel), language to use (ts or js use the one that you prefer)

![directus-extension cli questions](https://user-images.githubusercontent.com/461124/218868382-821fb1b5-6932-4e2f-8a28-74607aed3217.PNG)

Now we're ready to start writing some code. Yes, directus was built with VueJs so you need to know the framework. Don't worry if you have experience with React/Angular learning VueJs is going to be easy for you.

![directus extension folder structure](https://user-images.githubusercontent.com/461124/218868390-4b75c645-9714-41b6-8096-21ee7712cf03.PNG)

## Installing ChartJs

Let's install the dependencies using npm as usual, in this case the dependencies are two: [chart.js](https://www.chartjs.org/) and [vue-chartjs](https://vue-chartjs.org/), but remember going to the folder **piechart-panel** first.

```
cd piechart-panel
npm i vue-chartjs chart.js
```

ok after that we can run the **dev** script so every time we changed the code a file **index.js** will be generated in the **dist** folder. That file is going to have all the necessary code to show our custom panel in the directus admin.

```bash
npm run dev
```

## Adding Pie component to the custom panel

Let's open the **panel.vue** file and in the script section let's add the chartjs imports and setup.

```js
import { Chart as ChartJS, ArcElement, Tooltip, Legend } from "chart.js";
import { Pie } from "vue-chartjs";

ChartJS.register(ArcElement, Tooltip, Legend);
```

Pretty simple, we're just telling ChartJs all the components that we're going to use. In this case **ArcElement, Tooltip, Legend** are required to render a Pie.

After that we included in the **components** object of our component the **Pie** component from Vue Chartjs.

```js
export default defineComponent({
  components: {
    Pie,
  },
  // rest of the component config.
```

This is how our script section is going to look:

```vue
<script lang="ts">
import { defineComponent } from "vue";
import { Chart as ChartJS, ArcElement, Tooltip, Legend } from "chart.js";
import { Pie } from "vue-chartjs";

ChartJS.register(ArcElement, Tooltip, Legend);

export default defineComponent({
  components: {
    Pie,
  },
  props: {
    showHeader: {
      type: Boolean,
      default: false,
    },
    text: {
      type: String,
      default: "",
    },
  },
});
</script>
```

Ok we can go to the template section, let's include our Pie component.

```vue
<template>
  <div class="text" :class="{ 'has-header': showHeader }">
    <Pie :data="chartData" />
  </div>
</template>
```

The **Pie** components needs a **data** prop, let's define it in the data section of our component:

```js
export default defineComponent({
  data() {
    return {
      chartData: {
        labels: ["Perú", "Spain", "Rusia"],
        datasets: [
          {
            backgroundColor: ["#41B883", "#E46651", "#00D8FF"],
            data: [40, 20, 80],
          },
        ],
      },
    };
  },
  // rest of the component config.
}
```

Here we're returning the **chartData** object to be used for our Pie component. Chartjs expects a **labels** and **datasets** array. In this case our Pie chart is going to show 3 different countries: 40% of the pie belongs to Perú, 20% to Spain and the rest to Rusia.

## Linking custom panel to docker container

Ok so our custom panel is ready to go, [according the docs](https://docs.directus.io/extensions/creating-extensions.html#local-extension) the installation is basically copying your code inside the **extensions** folder. But not all the files just the **index.js** generated in the **dist** folder.

To have a better understanding of this we need to know the folder structure of a directus project:

```
+-- database
+-- extensions
|   +-- displays
|   +-- endpoints
|   +-- hooks
|   +-- interfaces
|   +-- layouts
|   +-- modules
|   +-- operations
|   +-- panels
        // here we must create our folder "piechart-panel"
|       +-- piechart-panel
          // our index.js file from the dist folder
|         +-- index.js
+-- uploads
+-- package.json
```

So when we executed the **npm run dev** command we already have a **index.js** file in the **dist** folder. We can link that folder to the one we need inside the docker container by creating a new **volume**.
Let's open our **docker-compose.yaml** file and add the extra line:

```yaml
volumes:
  - ./database:/directus/database
  # telling docker that link our dist folder inside piechart-panel to one with the same name inside the docker container in the extensions/panel folder.
  - ./piechart-panel/dist:/directus/extensions/panels/piechart-panel
```

Now let's restart the container

```bash
docker-compose restart
```

And finally we can create a new dashboard and add our new custom panel.

![Creating a dashboard in directus](https://user-images.githubusercontent.com/461124/218847808-f42eeab6-c684-4ac6-8b67-e2f7de9b3ca4.gif)

## Changing the options of the custom panel

Now probably you're wondering if we can change the name of our panel and the little description and of course we can.

![](https://user-images.githubusercontent.com/461124/218868385-3e69be0b-5cce-4644-a049-d9dd6e2e7e30.PNG)

Let's open the **index.ts** file and inside of it let's update the **id, name, icon and description** attributes.

```js
export default definePanel({
  id: "chartjs-pie",
  name: "ChartJs Pie",
  icon: "pie_chart",
  description: "A quick implementation of a pie with chartjs",
  // rest of the object
});
```

Restart the project one more time:

```
docker-compose restart
```

And if we try to create a new panel we can see our changes

![](https://user-images.githubusercontent.com/461124/218868388-8dc5b3a0-3a03-49c2-bd00-717f2940087d.PNG)

And that's it! In a real world application you don't want to use hardcode data so in a future post I will cover that part linking our custom panel to a collection through the UI.

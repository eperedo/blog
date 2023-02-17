---
title: Accessing items of a collection from a custom panel in Directus
date: 2023-02-15 20:35:00
---

In a [previous post](https://blog.eperedo.com/2023/02/14/custom-panel-directus-chart-js/) I showed you how to create a custom panel for your insights on Directus. Since it was a simple example I used hardcode data for the chart. Of course in a real application you can't use hardcode information even if you're really fast updating the content.

## TL;DR

If you are already a directus expert you can clone the full [code of this post here](https://github.com/eperedo/directus-chartjs-panel/tree/post-02)

## Running our dev command to compile our panel on every change

So let's update our custom extension to use data from a directus collections instead just hardcode data. Before starting let's run the **dev** command:

```bash
cd piechart-panel
npm run dev
```

## Accessing the collection from a vue component

For this example I'm going to create a user collection with 3 fields: **id, name and country.**

![directus user collection page](https://user-images.githubusercontent.com/461124/219236438-facdd298-a469-4167-b060-82807666fb9d.PNG)

And now I'm going to import from a [csv file with some data](https://github.com/eperedo/vue-static-map/files/10751036/users.csv) for our collection

![directus user list](https://user-images.githubusercontent.com/461124/219236460-a86a35c5-cbf1-468e-a314-129e00901e1d.PNG)

Let's open our **panel.vue** file and add the code that allow fetching the data from the **user** collection.
Since our component is just part of a whole Vue application directus already include a provider for accessing the API. The provider is called **api**.

```ts
export default defineComponent({
  inject: ["api"],
  // rest of the component config.
});
```

now we have the **api** object in our instance, let's add the code to make the http request and get the information from the **user** collection. We're going to do this in the **mounted** function.

```ts
export default defineComponent({
  inject: ["api"],
  mounted() {
    this.api
      .get("/items/user?aggregate[count]=id&groupBy[]=country")
      .then((res: any) => {
        console.log("Response", res);
      });
  },
  // rest of the component config.
});
```

directus already provide [documentation for accessing the items of a collection](https://docs.directus.io/reference/items.html). For our example the url have the segments:

- **/items** - because we're trying to get items of a collection
- **/user** - because that's the name of the collection (can be anything you want at the moment of creating it)
- **?aggregate[count]=id&groupBy[]=country** - since we need the total of users per country this is similar to writing a sql query like this:

```sql
select count(id) from users group by country;
```

You can learn more about [Aggregation & Grouping filters in this link](https://docs.directus.io/reference/query.html#aggregation-grouping).

After all of this we're just logging the response to the console so we can check the structure:

![directus items response structure](https://user-images.githubusercontent.com/461124/219236551-3de6095a-e034-4514-b9b3-db26fe50b7b3.PNG)

ok since directus use [axios](https://axios-http.com/docs/intro) under the hood it's a very familiar structure (at least for me a former axios fanboy) we have a **data** object and inside of it a **data** array that includes all the items from the **user** colecction group by country.
Let's back a little bit and remember what's the data structure that chartjs needs:

```js
  chartData: {
    // here we need to add all the countries
    labels: [],
    datasets: [
      {
        backgroundColor: ["#41B883", "#E46651", "#00D8FF"],
        // here the total number of users per country
        data: [],
      },
    ],
  },
```

In order to make chartjs happy we can iterate the results in the response and in each iteration we can push the name of the country to the **labels array** and then get the total users per country from the **count** object.

```ts
  mounted() {
    this.api
      .get("/items/user?aggregate[count]=id&groupBy[]=country")
      .then((res: any) => {
        // in a real app of course don't use anys all over the place
        res.data.data.forEach((user: any) => {
          this.chartData.labels.push(user.country);
          this.chartData.datasets[0].data.push(user.count.id);
        });
      });
  },
```

we're almost done, the last thing to do is deal with the async nature of the operation, at the beginning the component is not going to have any data until the api send a response, so let's add a quick loading message while the request is in progress and only show the chart until the response is ready.
Our template is gonna change a bit:

```html
<template>
  <div class="text" :class="{ 'has-header': showHeader }">
    <Pie v-if="showChart" :data="chartData" />
    <p v-else="!showChart">Loading...</p>
  </div>
</template>
```

We're using a **showChart** variable that is going to start with a **false** value and once the request has finished we change the value to **true**.
Of course the **showChart** variable needs to be declared in our **data** inside the definition of the component

```ts
export default defineComponent({
  data() {
    return {
      // start in false so the loading message appears first
      showChart: false,
      chartData: {
        labels: [],
        datasets: [
          {
            backgroundColor: ["#41B883", "#E46651", "#00D8FF"],
            data: [],
          },
        ],
      },
    };
  },
  mounted() {
    this.api
      .get("/items/user?aggregate[count]=id&groupBy[]=country")
      .then((res: any) => {
        res.data.data.forEach((user: any) => {
          this.chartData.labels.push(user.country);
          this.chartData.datasets[0].data.push(user.count.id);
        });
        // after we now all the data structure is ready we change
        // the value to true
        this.showChart = true;
      });
  },
});
```

Ok we're ready to give it a try, let's restart our container

```bash
docker-compose restart
```

and now we can see the final result, our chartjs custom panel using information from a directus collection

![custom directus panel using data from collection](https://user-images.githubusercontent.com/461124/219236592-cdc9583e-c847-4fe2-8169-a68d15f92c9e.PNG)

That's it! Pretty simple to interact with the API and get information from a collection. Here's the full code for the **panel.vue** file.

```html
<template>
  <div class="text" :class="{ 'has-header': showHeader }">
    <Pie v-if="showChart" :data="chartData" />
    <p v-else="!showChart">Loading...</p>
  </div>
</template>

<script lang="ts">
  import { defineComponent } from "vue";
  import { Chart as ChartJS, ArcElement, Tooltip, Legend } from "chart.js";
  import { Pie } from "vue-chartjs";

  ChartJS.register(ArcElement, Tooltip, Legend);

  export default defineComponent({
    data() {
      return {
        showChart: false,
        chartData: {
          labels: [],
          datasets: [
            {
              backgroundColor: ["#41B883", "#E46651", "#00D8FF"],
              data: [],
            },
          ],
        },
      };
    },
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
    inject: ["api"],
    mounted() {
      this.api
        .get("/items/user?aggregate[count]=id&groupBy[]=country")
        .then((res: any) => {
          res.data.data.forEach((user: any) => {
            this.chartData.labels.push(user.country);
            this.chartData.datasets[0].data.push(user.count.id);
          });
          this.showChart = true;
        });
    },
  });
</script>

<style scoped>
  .text {
    padding: 12px;
  }

  .text.has-header {
    padding: 0 12px;
  }
</style>
```

Would be great if the users of the admin could have the possibility of choosing a collection and not be restricted to just the user collection like in this demo, but that's for a future post.

Full [code on github](https://github.com/eperedo/directus-chartjs-panel/tree/post-02)

Hope this post help you!

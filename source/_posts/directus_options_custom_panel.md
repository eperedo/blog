---
title: Adding options for a custom panel in Directus
date: 2023-02-16 23:28:00
---

Continuing [with our custom panel](https://blog.eperedo.com/2023/02/16/accessing_items_collection_from_custom_panel_directus/) we already saw how to bind a collection to our pie chart, but there's something we can do better. If you see other panel like the one that displays a [Time Series](https://docs.directus.io/app/insights.html#time-series) you actually can see that it gives you the possibility to select a collection and different options for your panel so you can customize it according your needs.

![directus time series modal](https://user-images.githubusercontent.com/461124/219549310-426ab8b9-edeb-4ead-bcad-5db9482308bb.PNG)

So in this post I'm going to implement something like that.

## TL;DR

If you are already a directus expert you can clone the full [code of this post here](https://github.com/eperedo/directus-chartjs-panel/tree/post-03)

## Defining your options

Right now our custom panel is going to always show you a pie chart of all the users grouped by country.
If we want to make this panel a little more generic we need to include two options: one for selecting the collection (this is basically the table that contains all the data) and of course also we need to give the ability to select the group field so we don't leave the country field in hardcode like we have right now.

## Start our dev command

Before writing anything you should run the **dev** command so we can compile our code everything we make a change:

```bash
cd piechart-panel
npm run dev
```

And of course the docker container should be running in the root of our project we can just execute:

```bash
docker-compose --project-name chartjs up -d
```

## Adding options to our custom panel

We've doing most of the changes in our **panel.vue** file, but now we're going to open our **index.ts** since this is the one that contains all the information about the options of a panel.

In this file under the **options** array we need to define our two dropdowns: one for selecting the collection and another one that depends on it that is going to display all the fields of the selected collection.

Directus already have built in this component that renders a dropdown including all the collections that you have available in your directus app and it's called **system-collection**. This is not in the documentation - or at least I wasn't able to find it - but since directus is an open source project [I found it on the github repo](https://github.com/directus/directus/tree/main/app/src/interfaces/_system/system-collection).

Knowing this we can add the dropdown as an option for our custom panel with the following code:

```ts
export default definePanel({
  id: "chartjs-pie",
  // rest of the config omitted for brevity
  options: [
    {
      field: "collectionName",
      name: "Collection",
      meta: {
        interface: "system-collection",
      },
    },
  ],
  // rest of the config omitted for brevity
});
```

let's explain all the attributes:

- **field:** this can be anything you want and the value is going to be the one you used in the vue component to get the reference to it. In this case is **"collectionName"** because... well we want the collection's name.

- **name:** this is the label every user will read above the dropdown.

- **meta.interface:** the interface represent the UI you want to render. **system-collection** is a built in directus component that already deals with the process of getting all the collections and showing them in a dropdown.

ok this is going to show the dropdown with all the collections, but now we need to write the code to receive the value and use it in our **panel.vue**.

## Reading the value in the Vue component

All the **options** we define must be added as a props of our vue component with the same value we put it on the **field** attribute. In this case since we want the collection name we're going to add the **collectionName** prop.

```ts
  // rest of the config omitted for brevity
  props: {
    collectionName: {
      type: String,
    },
  }
```

pretty straightforward, now we can access the value of this prop like any ordinary prop in vuejs. For our example we have the **user** collection directly in the mounted method let's change that:

```ts
mounted() {
  let url = `/items/${this.collectionName}?aggregate[count]=id&groupBy[]=country`;
  this.api.get(url).then(
    // rest of the code omitted for brevity
  );
}
```

If we give it a try we can see the dropdown with our user collection because that's the only one we have right now

![directus dropdown listing collections](https://user-images.githubusercontent.com/461124/219549304-5ce5c8ff-ffd4-4833-beeb-06c8e3cfd010.PNG)

But remember we also want to allow the customization of the group field so our chart works with every set of data and not only for grouping by country. We need a second dropdown but in this case listing the fields related to the collection selected.

As you probably guess this is other **interface** already built on directus. Let's open our **index.ts** file again:

```ts
// rest of the code omitted for brevity
options: [
  {
    // the "field" attribute is important
    // because must be equal to the "collectionField" attribute.
    field: "collectionName",
    name: "Collection",
    meta: {
      interface: "system-collection",
    },
  },
  {
    field: "groupField",
    name: "Field",
    meta: {
      interface: "system-field",
      options: {
        // this one must be the same as the one in
        // options[0].field: 'collectionName'
        collectionField: "collectionName",
      },
    },
  },
];
```

very similar to the first option, but in this case the value for the **interface** attribute is **system-field** and this interface has its own options. One of them is the **collectionField** attribute that must match the **field** attribute of the **system-collection** that's why we have **collectionName** as a value here.

The rest of the process is the same we need to bind our new option to a prop in our vue component and in our **mounted** function.

```ts
  // rest of the code omitted for brevity
  props: {
    collectionName: {
      type: String,
    },
    groupField: {
      type: String,
    },
  },
  mounted() {
    let url = `/items/${this.collectionName}?aggregate[count]=id&groupBy[]=${this.groupField}`;
    this.api
      .get(url)
      .then((res: any) => {
        res.data.data.forEach((colObj: any) => {
          this.chartData.labels.push(colObj[this.groupField]);
          this.chartData.datasets[0].data.push(colObj.count.id);
        });
        this.showChart = true;
      });
  },
```

The groupField prop is a string and we're changing the url to use the **groupField** variable for the **groupBy** segment.
Another minor change was the value that is being pushed to the labels array. It was the **country** attribute but since that value is dynamic now is being referenced to the prop **groupField** as well.

Now if we open our custom panel we can see both dropdowns and the second is being filled with all the values related to the collection selected.

![directus dropdown for collection's fields](https://user-images.githubusercontent.com/461124/219549309-f98885d8-bfbd-4670-83f5-8ed8f5edbcd7.PNG)

So for the big finale of this post let's add a different **interface**. We also want to give our users the ability to setup the colors of the chart.

## Adding a multiple dropdown interface

Directus have many interfaces you can reuse, this isn't part of the documentation right now, but again reading the code is free so you can [see all the components on github](https://github.com/directus/directus/tree/main/app/src/interfaces).

Since a chart can have many colors we're going to use the **select-multiple-dropdown** interface.
The first step is adding to the options array:

```ts
    // rest of the config omitted for brevity
    {
      field: "colors",
      name: "Select colors",
      meta: {
        interface: "select-multiple-dropdown",
        options: {
          choices: [
            { text: "Red", value: "#ff0000" },
            { text: "Blue", value: "#0000ff" },
            { text: "Green", value: "#00ff00" },
          ],
          allowOther: true,
        },
      },
    },
```

You already know the **field** and **name** attributes so let's cover the new ones:

- **options.choices:** an array of objects with the default values of our dropdown. The structure of the object always must include a **text** and a **value**. The first is the one that the user see and the **value** is the one you get in the vue component as a prop.

- **options.allowOther:** we don't want to limit our users to basic colors so this attribute is going to give that flexibility so everyone can add their favorite color.

and again in our vue component let's define the prop and update our code to push the values to the **backgroundColor** array that chartjs use.

```ts
    colors: {
      type: Array,
    },
  },
  mounted() {
    let url = `/items/${this.collectionName}?aggregate[count]=id&groupBy[]=${this.groupField}`;
    this.api.get(url).then((res: any) => {
      res.data.data.forEach((colObj: any) => {
        this.chartData.labels.push(colObj[this.groupField]);
        this.chartData.datasets[0].data.push(colObj.count.id);
        // the select-multiple-dropdown interface always
        // returns an array we can pass it directly
        this.chartData.datasets[0].backgroundColor = this.colors;
      });
      this.showChart = true;
    });
  },
```

And it's going to look like this:

![directus multiple dropdown](https://user-images.githubusercontent.com/461124/219549306-739227ee-12ae-48e4-8fb8-761d391cd2bc.PNG)

And you can see the "Other..." button where we can add more colors

![directus multiple dropdown more choices](https://user-images.githubusercontent.com/461124/219549307-c89602cc-a2cb-4bda-b2fb-5f498432d8b9.PNG)

We've seen how directus not just gives us great defaults but also how easy is to extend any feature with a few lines of code.

I'm going to be covering different ways to extend directus and not just panels, but that's for a next post.

Full [code on github](https://github.com/eperedo/directus-chartjs-panel/tree/post-03)

Hope this helps!

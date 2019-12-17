---
title: Sintaxis de Plantillas en Vue js
date: 2019-12-17 17:00:00
---

Vue js incorpora un sistema de plantillas muy simple y sencillo, pero bastante poderoso. Este sistema de plantillas es el que le permite a Vue interactuar con la [instancia de nuestra aplicación](https://blog.eperedo.com/2019/12/15/creando-una-instancia-en-vue-js/).

En los templates podemos combinar el html de toda la vida con [las variables declaradas en la propiedad **"data"**](https://blog.eperedo.com/2019/12/16/propiedad-data-vue-js/).

```html
<div id="my-app">
  <h1>{{name}}</h1>
  <p>{{lastname}}</p>
</div>

<script>
  const app = new Vue({
    el: "#my-app",
    data: {
      name: "Susana",
      lastname: "Horia"
    }
  });
</script>
```

En el template no solo se pueden mostrar valores sino también expresiones básicas de Javascript como operaciones matemáticas o concatenación de cadenas.

```html
<div id="my-app">
  <p>{{2*2}}</p>

  <p>{{`Hola mi nombre completo es ${{name}} ${{lastname}}</p>
</div>

<script>
  const app = new Vue({
    el: "#my-app",
    data: {
      name: "Susana",
      lastname: "Horia"
    }
  });
</script>
```

Adicionalmente se pueden ejecutar expresiones javascript siempre y cuando sean de un solo statement

```html
<div id="my-app">
  <p>{{ name.toUpperCase() }}</p>

  <p>{{ age >= 18 ? "Soy Mayor de Edad" : "Soy Menor de Edad" }}</p>

  <p>
    Mi sueldo es {{salary.toFixed(2)}}
  </p>
</div>

<script>
  const app = new Vue({
    el: "#my-app",
    data: {
      age: 18,
      lastname: "Horia",
      name: "Susana",
      salary: 250
    }
  });
</script>
```

Y a algunas variables globales como: Math, Number, Date, Array, Object, Boolean, String, RegExp, Map, Set, JSON, Intl.

```html
<div id="my-app">
  <p>
    La fecha es {{new Date().toDateString()}}
  </p>

  <p>
    2 al cuadrado es {{Math.pow(2, 2)}}
  </p>
</div>
```

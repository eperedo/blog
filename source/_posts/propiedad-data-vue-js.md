---
title: La propiedad data en vue js
date: 2019-12-16 12:00:00
---

Ya generamos [una instancia en Vue.Js](https://blog.eperedo.com/2019/12/15/creando-una-instancia-en-vue-js/), pero nuestra aplicación realmente no hace
nada interesante.
Vamos a cambiar el título de nuestra aplicación para generarlo de manera dinámica
desde Vue en lugar de que esté directamente en el archivo html.

Así como tenemos una propiedad **"el"** que vue usa para saber que parte de nuestra app controlar, existe otra propiedad **"data"** que sirve para definir todos los datos que queremos que Vue tenga disponible para mostrarlos en nuestro html.

Vamos a definir el objeto data con una propiedad llamada **title** y le daremos el valor que actualmente tenemos en nuestro html.

```js
const app = new Vue({
  el: "#my-app",
  data: {
    title: "Nuestra App con VueJs"
  }
});
```

Listo, ahora queremos que el valor de **title** se muestre en nuestro html, para eso
vamos a hacer uso de la sintaxis de plantillas de Vue. Para mostrar variables definidas en **data** dentro del html tenemos que usar el simbolo de llaves "{{}}" y
dentro de ellas el nombre de la variable

```vue
{{ title }}
```

El resultado completo se vería así.

```html
<body>
  <div id="my-app">
    {{title}}
  </div>

  <script>
    const app = new Vue({
      el: "#my-app",
      data: {
        title: "Nuestra App con VueJs"
      }
    });
  </script>
</body>
```

Listo, ahora ya sabemos como mostrar en nuestro html valores definidos en nuestra instancia de Vue 🔥

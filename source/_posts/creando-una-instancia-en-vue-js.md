---
title: Creando una instancia en Vue js
date: 2019-12-15 16:10:00
---

Para crear una aplicación con VueJs lo primero que necesitamos hacer es incluir el script en nuestro archivo html. Vamos a usar el CDN [jsdelivr](https://www.jsdelivr.com/) para no tener que descargar el script a nuestra computadora.

```html
<!DOCTYPE html>
<html lang="es">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />

    <title>Creando una instancia en VueJs</title>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  </head>
  <body>
    <div id="my-app">
      Nuestra App con VueJs
    </div>
  </body>
</html>
```

Una vez agregado el script ya podemos generar una nueva instancia de Vue
usando el operador **new** y enviando como parámetro un objeto de configuración.
Dentro de esta configuración tenemos muchas opciones, pero por ahora la más
importante es la propiedad **el** que acepta como valor cualquier selector CSS.

```html
<body>
  <div id="my-app">
    Nuestra App con VueJs
  </div>

  <script>
    const app = new Vue({
      el: "#my-app"
    });
  </script>
</body>
```

En este caso le estamos diciendo a Vue que controle todo lo que esté dentro **del div con id "my-app"**. Listo, ya tenemos nuestra instancia configurada correctamente y con ello nuestra primera aplicación basada en VueJs :fire:

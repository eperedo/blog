---
title: Directivas en Vue js
date: 2019-12-25 16:10:00
---

Las directivas en VueJs son atributos especiales que permiten enlazar de manera
reactiva algun valor del [data](https://blog.eperedo.com/2019/12/16/propiedad-data-vue-js/) con el [template](https://blog.eperedo.com/2019/12/17/sintaxis-templates-plantillas-vue-js/).

Cuando los valores cambien las directivas son las encargadas de llevar dichos cambios al DOM.

Vue Js incorpora muchas directivas por defecto:

### v-if

Agrega o elimina un elemento del DOM.

```html
<div v-if="showAlert">
  <p>Hello, you must readme me!</p>
</div>

<script>
  const app = new Vue({
    el: "#my-app",
    data: {
      showAlert: true
    }
  });
</script>
```

En el ejemplo anterior tenemos un div que se va a mostrar cada vez que la variable **_showAlert_** evalúe a **_true_** y se eliminará del DOM si es que la evaluación es **_false_**.

### v-show

Esta directiva funciona similar a v-if, pero con la diferencia de que en lugar de agregar/eliminar el elemento del DOM agregará la propiedad css **_display_** con el valor **_none_** para ocultarlo. En caso el resultado de la expresión sea falsa eliminará la propiedad.

```html
<div v-show="showAlert">
  <p>Hola, leemé por favor!</p>
</div>

<script>
  const app = new Vue({
    el: "#my-app",
    data: {
      showAlert: true
    }
  });
</script>
```

### v-html

Esta directiva permite insertar html en nuestro template. Es decir que actualizará la propiedad [innerHTML](https://developer.mozilla.org/en-US/docs/Web/API/Element/innerHTML) del elemento.

```html
<div v-html="myCustomHtml"></div>

<script>
  const app = new Vue({
    el: "#my-app",
    data: {
      myCustomHtml: "<p>Hola <strong>Mundo usando v-html!</strong></p>"
    }
  });
</script>
```

### v-text

Similar a **v-html**, pero actualiza la propiedad **textContent** del elemento

```html
<p v-text="myText"></p>

<script>
  const app = new Vue({
    el: "#my-app",
    data: {
      myText: "Hola Mundo usando v-text!"
    }
  });
</script>
```

### v-bind

Permite unir algún valor de nuestro data a un atributo.

```html
<img v-bind:src="myPhotoUrl" alt="Nicolas Cage Photo" />

<script>
  const app = new Vue({
    el: "#my-app",
    data: {
      myPhotoUrl: "https://www.placecage.com/200/300"
    }
  });
</script>
```

Sirve para cualquier atributo, por ejemplo **style**

```html
<p v-bind:style="myStyle">Actualiza la pagina para verme de otro color</p>

<script>
  const app = new Vue({
    el: "#my-app",
    data: {
      myStyle: Math.random() * 2 > 1 ? { color: "red" } : { color: "green" }
    }
  });
</script>
```

### v-pre

Le indica a Vue que no debe realizar ningún proceso de compilación en este elemento y cualquiera de sus hijos.

```html
<div v-pre>
  <p v-if="false">Las directivas aca no tienen poder</p>
  <div>
    <p>{{preValue}}</p>
  </div>
</div>

<script>
  const app = new Vue({
    el: "#my-app",
    data: {
      preValue: "Este mensaje no se verá en el template"
    }
  });
</script>
```

### v-once

Renderiza el elemento solo una vez. Si la expresión asociada al elemento cambia no se verá reflejada en el template.

```html
<p v-once>{{onceMessage}}</p>

<script>
  const app = new Vue({
    el: "#my-app",
    data: {
      onceMessage: "Este mensaje nunca cambiará"
    }
  });
</script>
```

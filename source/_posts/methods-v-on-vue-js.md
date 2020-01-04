---
title: Métodos y v-on en Vue js
date: 2020-01-04 16:34:00
---

Así como se puede configurar una propiedad **[data](https://blog.eperedo.com/2019/12/16/propiedad-data-vue-js/)** dentro de una **[instancia de vue](https://blog.eperedo.com/2019/12/15/creando-una-instancia-en-vue-js/)** también tenemos otra propiedad importante:

### Methods

Esta propiedad nos permite declarar funciones que pueden ser invocadas
dentro de **[una plantilla](https://blog.eperedo.com/2019/12/17/sintaxis-templates-plantillas-vue-js/)** gracias al uso de la **[directiva](https://blog.eperedo.com/2019/12/25/directives-vue-js/)** **v-on**.

```html
<div id="app">
  <button type="button" v-on:click="saludar">
    Saludar
  </button>
</div>
<script>
  new Vue({
    el: "#app",
    methods: {
      saludar: function() {
        alert("Hola");
      }
    }
  });
</script>
```

La directiva acepta como string el nombre de algún método creado en nuestra propiedad **methods**.

### Pasar parámetros a un método usando v-on

En el ejemplo anterior simplemente indicamos el nombre del método, pero
imaginemos que queremos pasar algún parámetro desde nuestro template al método, tendríamos que enviar el parámetro tal como cuando invocamos un método en el javascript de toda la vida.

```html
<div id="app">
  <button type="button" v-on:click="saludar('Eduardo')">
    Saludar
  </button>
</div>
<script>
  new Vue({
    el: "#app",
    methods: {
      saludar: function(name) {
        alert("Hola" + name);
      }
    }
  });
</script>
```

### Accediendo a información del evento

Cuando se genera un evento en javascript siempre se incluye información del mismo en un objeto. En Vue también podemos acceder a este usando una variable especial llamada **\$event**.

```html
<div id="app">
  <button type="button" v-on:click="saludar($event)">
    Saludar
  </button>
</div>
<script>
  new Vue({
    el: "#app",
    methods: {
      saludar: function(event) {
        alert("Coordenadas del botón:" + event.screenX + " " + event.screenY);
      }
    }
  });
</script>
```

También podemos hacer una combinación de parámetros:

```html
<div id="app">
  <button type="button" v-on:click="saludar($event, 'Eduardo')">
    Saludar
  </button>
</div>
<script>
  new Vue({
    el: "#app",
    methods: {
      saludar: function(event, name) {
        alert("Coordenadas del botón:" + event.screenX + " " + event.screenY);
        alert(name);
      }
    }
  });
</script>
```

Por último cuando a **v-on** solo le indicamos el nombre del método vue envía por defecto todos los parámetros existentes de ese evento. Eso quiere decir que al hacer esto:

```html
<div id="app">
  <!-- solo indicamos el nombre del método -->
  <button type="button" v-on:click="saludar">
    Saludar
  </button>
</div>
```

Ya podremos acceder a la variable **\$event** en nuestro método:

```js
new Vue({
  el: "#app",
  methods: {
    saludar: function(event) {
      alert("Coordenadas del botón:" + event.screenX + " " + event.screenY);
    }
  }
});
```

Si el evento **click** enviara más parámetros también lo tendríamos disponible en la definición de nuestro método.

### Accediendo a data desde los métodos

Dentro de los métodos podemos acceder a cualquier variable definida en nuestro objeto **data** usando **this** más el nombre de la variable que deseamos usar.

```html
<div id="app">
  <button type="button" v-on:click="showFullName">
    Mostrar Nombre
  </button>
</div>
<script>
  new Vue({
    el: "#app",
    data: {
      firstName: "Eduardo",
      lastName: "P. Rivero"
    },
    methods: {
      showFullName: function() {
        alert(this.firstName + " " + this.lastName);
      }
    }
  });
</script>
```

También podemos asignar valores a las variables como lo haríamos con cualquier variable en javascript.

```html
<div id="app">
  <button type="button" v-on:click="doubleAge">
    Duplicar mi Edad
  </button>
  <p>
    Mi edad es {{age}}
  </p>
  <p v-if="ageByTwo">
    Mi edad duplicada es {{ageByTwo}}
  </p>
</div>
<script>
  new Vue({
    el: "#app",
    data: {
      firstName: "Eduardo",
      lastName: "P. Rivero",
      age: 33,
      ageByTwo: 0
    },
    methods: {
      doubleAge: function() {
        // Asignamos nuevo valor a variable
        this.ageByTwo = this.age * 2;
      }
    }
  });
</script>
```

Aunque vimos los ejemplos usando el evento **click** vue soporta [cualquier evento del DOM](https://www.w3schools.com/jsref/dom_obj_event.asp)

### Alias @

Para ahorrarnos algunos caractéres existe una forma corta de referenciar a un método usando **v-on** mediante el símbolo **@**

```html
<div id="app">
  <button type="button" @click="saludar">
    Saludar
  </button>
</div>
```

Esto cubre lo básico sobre los métodos en Vue, nunca está de más [leer la documentación para ahondar en este y otros conceptos](https://vuejs.org/v2/api/#v-on). 🚀

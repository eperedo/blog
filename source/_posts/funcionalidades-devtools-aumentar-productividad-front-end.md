---
title: Funcionalidades de Devtools para aumentar productividad como Front End developer
date: 2021-05-20 17:00:00
---

### 💻 Variables $0 al $4

En la pestaña **Elements** del devtools puedes seleccionar cualquier etiqueta html y al hacerlo automaticamente devtools asigna esa etiqueta a una variable **$0**. Si vas a la pestaña de **Console** y escribes **$0** verás que es una variable con acceso a todos los métodos y atributos del elemento html seleccionado como **innerText**, **addEventListener**, etc.

Adicionalmente existen otros _shortcuts_ como **$1**, **$2**, **$3**, **$4**. En estas variables el devtools guardará los últimos 4 elementos que selecciones en el tab **Elements**.

### 🔍 Shortcuts para querySelector y querySelectorAll

Imagina que quieres saber cuantos botones hay en tu html, puedes usar querySelectorAll para averiguarlo:

```js
var buttons = document.querySelectorAll("button");
console.log(buttons.length);
// muestra el total de elementos
```

Gracias a devtools puedes rápidamente reemplazar esta funcion por el doble dolar **$$**

```js
var buttons = $$("button");
console.log(buttons.length);
// muestra el total de elementos
```

Tener en cuenta que $$ al ser una utilidad del devtools retornará un arreglo, mientras que **querySelectorAll** retorna un **NodeList**. Pueden comprobarlo con el siguiente código:

```js
var buttons = $$("button");
Array.isArray(buttons); // retorna true

var buttons = document.querySelectorAll("button");
console.log(buttons); // retorna false
```

Para el caso de **querySelector**, su alias es **$** y también es una función a la que debemos pasarle un selector, ambas funciones retornan un elemento del dom y no hay un cambio en el tipo de dato como en **querySelectorAll**

**Tener en cuenta que al ser variables globales pueden ser reemplazadas fácilmente, por ejemplo si tienes jQuery en tu html el **$** será jQuery y no querySelector**

### ✏️ Copiar una variable

Vamos a suponer que tenemos una función que tras realizar una llamada http retorna un objeto que representa la información de un usuario

```js
function fetchUser() {
  var user = {
    id: 1,
    name: "Eduardo",
    age: 33,
    country: "Peru",
    salary: 100,
  };
  return user;
}
```

Ahora imagina que necesitas todo ese objeto para pegarlo en otra herramienta y hacer algún proceso con esa información, lo normal sería hacer un console.log al objeto, seleccionarlo y copiarlo. ¡Aburrido! Devtools te ahorra ese proceso con su función **copy**

```js
function fetchUser() {
  var user = {
    id: 1,
    name: "Eduardo",
    age: 33,
    country: "Peru",
    salary: 100,
  };
  copy(user); // ahora tienes este objeto en tu porta papeles
  return user;
}
```

Listo, cuando ejecutes la función podrás hacer ctrl/cmd + V y pegar el objeto en cualquier lado.

### 🕵️ Seleccionar elementos vía código con inspect

Buscar y seleccionar un elemento es muy simple con devtools, simplemente haces click en el icono de la flecha con el cuadradito y luego vas al html y haces click sobre el elemento que deseas, fácil. Pero hay una manera _fancy_ de hacerlo desde código gracias al método **inspect**.
Este método acepta como parámetro un elemento del dom.

```js
var button = document.querySelector("#my-button");
inspect(button);
```

Al ejecutarse la función inspect automaticamente el devtools cambiará al tab **Elements** y te mostrará el elemento que corresponda al selector **my-button**.

### 👀 Monitorear una función

¿Heredaste un código larguísimo y difícil de entender? ¿Las funciones y variables tienen nombres de letras como **x**, **z**? ¿Estás leyendo esto con voz de vendedor de teleferia? No te preocupes, con la función **monitor** devtools te avisará cuando alguna función de tu código ha sido llamada.

En el siguiente código veremos a una función que retorna un simple objeto

```js
function fetchUser() {
  var user = {
    id: 1,
    name: "Eduardo",
    age: 33,
    country: "Peru",
    salary: 100,
  };
  return user;
}
```

Si deseo que devtools me avise cada vez que se ha ejecutado la función solo debo hacer lo siguiente:

```js
monitor(fetchUser);
// Ejecutamos la función
fetchUser();
// Nos aparecerá el siguiente mensaje:
// function fetchUser called
```

### 📷 Tomar screenshots

Quieres pasarle un screenshot a alguien de un elemento del DOM específico. Pues simple, selecciono el elemento del DOM usando el devtools, y luego presiono **ctrl + shit + p** se abrirá un modal con un listado de comandos de acciones del devtools, escribimos "capture node screenshot" y automaticamente se guardará la imagen del nodo seleccionado. Adicionalmente se puede generar screenshot de toda la pantalla o incluso dar la opción de una seleccionar una parte en específico.

![](https://res.cloudinary.com/drukp4ipu/image/upload/v1586282483/blog/devtools/devtools-capture-screenshot.gif)

Y así como estás funcionalidades hay muchas más. ¿Conoces alguna otra funcionalidad? ¡Compártela en los comentarios!

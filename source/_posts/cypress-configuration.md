---
title: Cypress Configuration
date: 2018-09-27 23:00:00
---

Cypress me da una configuración por defecto para poder enfocarme desde el inicio en solo escribir las pruebas y aunque inicialmente es algo bueno no todas las convenciones son las que quiero manejar.

### Cypress.json

En el root del proyecto se puede crear un archivo **cypress.json** y cambiar los valores por defecto de la configuración de cypress. Lo primero que quiero configurar es la url de mi servidor ya que es bastante obvio que a más pruebas tenga más voy a tener que repetirla por muchas de ellas. Dentro del json de configuración agrego el valor **baseUrl**. Otra cosa que quiero cambiar es la ruta donde se guardan los [screenshots y videos](https://blog.eperedo.com/2018/09/27/cypress-video-screenshots/) ya que quiero que se creen en una carpeta distinta a la por defecto. Como con la url base simplemente pongo la ruta de ambas opciones usando las propiedades **screenshotsFolder** y **videosFolder**.

```json
{
  "baseUrl": "http://localhost:8080",
  "screenshotsFolder": "tests/schreenshots",
  "videosFolder": "tests/videos"
}
```

Por defecto cypress busca en el root del proyecto el archivo cypress.json así que no hay que hacer nada especial para que reconozca este archivo. Si ejecuto mis pruebas:

```bash
# forma corta de npm test
npm t
```

Las pruebas fallan porque al incluir un valor para **baseUrl** cypress usa esa url y la agrega como prefijo a los métodos **visit** y **request**. Eso significa que en mi [command **login**](https://blog.eperedo.com/2018/09/25/cypress-testing-commands/) debo indicarle sobre que url haré mis pruebas. En este caso mi formulario de login se encuentra en la url principal por lo que simplemente indicando un slash se soluciona mi problema.

```javascript
// cypress/command.js
Cypress.Commands.add("login", ({ email, password }) => {
  // El slash le indica que vaya a la url principal
  cy.visit("/");
  cy.get("input#email-input").type(email);
  cy.get("input#pass-input").type(password);
  cy.get("button#btn-login").click();
});
```

Ya que las pruebas fallaron cypress genera automáticamente _screenshoots_ de mis pruebas fallidas. Esto confirma que la configuración en **cypress.json** funciona correctamente ya que se ha creado la carpeta **tests/screenshoots** y dentro de esa carpeta las imágenes de las pruebas que fallaron.
Ahora si ejecuto nuevamente las pruebas y todo funciona sin problemas.

```bash
npm t
```

Para probar si la configuración de la carpeta de videos funciona debo ejecutar mis pruebas usando Electron como browser ya que es el único que soporta la grabación de los mismos. Para eso ya tengo un script así que solo queda ejecutarlo.

```bash
npm run test:video
```

Luego de terminar las pruebas veo el siguiente _output_

```bash
(Video)

  - Started processing:   Compressing to 32 CRF
  - Finished processing:  cypress-course/tests/videos/login.spec.js.mp4 (2 seconds)
====================================================================================================
```

La ruta me confirma que los videos se crearon en la misma carpeta que configure en el json. Hay muchos [valores que se pueden configurar](https://docs.cypress.io/guides/references/configuration.html#Options) como variables de entornos que me van a servir cuando integre esto con [circle ci](https://circleci.com)

Repo con el código: https://github.com/eperedo/cypress-learning

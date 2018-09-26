---
title: Cypress Video/Screenshots
date: 2018-09-26 23:00:00
---

Ahora que puedo [ejecutar mis tests en la terminal](https://blog.eperedo.com/2018/09/26/cypress-command-line/) descubrí un _feature_ bastante interesante que tiene Cypress: generar videos de los tests que se ejecutan.

### Cypress Video

Esta funcionalidad solo se activa cuando se ejecutan los tests usando Electron como browser. Es bastante parecido a ejecutarlo con chrome en realidad solo cambia el nombre del parámetro browser. Más fácil aún ya que el valor por defecto es Electron así que el comando quedaría así:

```bash
cypress run --spec 'cypress/integration/login.spec.js
```

Y como yo me olvido de todo voy a crear mi script npm

```json
{
  "scripts": {
    "test:video": "cypress run --spec 'cypress/integration/login.spec.js'"
  }
}
```

Ejecuto **npm run test:video** y las pruebas se ejecutan normalmente, pero al finalizar puedo ir a la carpeta **cypress/videos** y ver un video de todas las pruebas que ejecuté.

### Cypress Screenshots

Adicionalmente a los videos Cypress viene con una funcionalidad que permite generar screenshots. Para activarla se puede llamar al método **cy.screenshot()** dentro de cualquier de los tests y se guardará la imagen dentro de la carpeta **cypress/screenshots**.
Adicionalmente cada vez que se ejecutan las pruebas usando **cypress run** se van a generar screenshots para los tests que fallen.

Repo con el código: https://github.com/eperedo/cypress-learning

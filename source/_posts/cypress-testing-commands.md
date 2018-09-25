---
title: Custom Commands en Cypress
date: 2018-09-22 23:00:00
---

Revisando las [pruebas que escribí](https://blog.eperedo.com/2018/09/23/cypress-testing/) me doy cuenta que estoy repitiendo una cantidad absurda de código y se siente un poco mal. Fácil podría extraer el código que se repite en una función, pero revisando la documentación de cypress ya existe una forma de hacerlo a la cypress.

### Custom Commands

Al parecer Custom commands son formas de agrupar funcionalidad que puede llegar a ser repetitiva en los tests. Cypress viene con varios commands pre definidos como: **visit**, **request**, **route**, etc.
En las pruebas que escribí se repite la funcionalidad de abrir la página de login, ingresar las credenciales y hacer click sobre el botón submit.

### Creando un custom command

Crearlo parece sencillo, dentro de la estructura que Cypress propone existe una carpeta **support** con dos archivos dentro: index.js y commands.js.
En commands.js voy a agregar el código que tengo repetido dentro de mis dos pruebas y queda algo así:\

```js
Cypress.Commands.add("login", ({ email, password }) => {
  cy.visit("http://localhost:8081");
  cy.get("input#email-input").type(email);
  cy.get("input#pass-input").type(password);
  cy.get("button#btn-login").click();
});
```

El método **Cypress.Commands.add** recibe dos parámetros: un nombre y una función que va a ejecutar el código que deseamos reutilizar. A la función le paso como parámetro un objeto que tenga las propiedades email y password y así poder usarlo sin problemas para los dos casos que tengo.

Usar el command es igual que usar un command "nativo" de cypress. Las pruebas quedan más cortas ya que mucha de su funcionalidad está encapsulada en el command **login**.

```js
describe("Formulario de Login", () => {
  it("Ingresando credenciales incorrectas", () => {
    const user = {
      email: "fake@email.com",
      password: "theFakePass123"
    };
    cy.login(user);
    // Verificar que aparezca el mensaje indicando el error.
    cy.contains("p", "Así no se puede, credenciales incorrectas");
  });

  it("Ingresando credenciales correctas", () => {
    const user = {
      email: "admin@mail.com",
      password: "theRealPass123"
    };
    cy.login(user);
    // Verificar que la nueva ruta es /welcome
    cy.url().should("include", "/welcome");
    cy.contains("h1", "Bienvenido!");
  });
});
```

La sensación de sufrimiento y dolor disminuye ya que mucho código que estaba repetiendo ha sido refactorizado en un command. Lo que me gusta de los commands es que a diferencia de extraerlo en un archivo cualquier no tengo que incluir una referencia a él en cada archivo mediante "import" o "require".

### Probando custom command

Bueno para finalizar ejecuto el script para abrir el panel de Cypress

```bash
npm run co
```

Las dos pruebas pasan sin problemas comprobando que el command fue creado de forma correcta.

Repo con el código: https://github.com/eperedo/cypress-learning
